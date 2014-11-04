---
date: "2012-05-17 16:57:22+0800"
layout: post
title: "WP7页面刷新的深度探索"
categories: 技术	
tags: WP7 Navigation refresh
---

首先说明一下，WP7 更新数据应该采用ObservableCollection来实现，按照WP7的设计意图，是不需要频繁的刷新页面的，数据的更新已经有很完美的实现方式。但是世上就有稀奇古怪的需求，也有稀奇古怪的不爽，前段时间自定义了一个控件，没搞明白怎么实时更新数据源，又不能强迫用户先退出再进入，于是乎，就想到了用个变态的方法实现页面的重新载入。

首先想到有一个现成的页面导航：

`NavigationService.Navigate(new Uri("/Page1.xaml", UriKind.Relative));`

在数据变化结束后重新导航到这个页面，试了下没效果。为什么不能像同页面导航呢，于是想到既然导航是基于URL的，而URL又是可以带参数的，那么带个参数对系统来说，就不是同一个URL了，但实际上还是同一个页面，试了下

`NavigationService.Navigate(new Uri("/Page1.xaml?flag=text", UriKind.Relative));`

果然就刷新了页面，一阵狂喜，不停地点击，又发现问题了，只有第一次更改数据时能导航过去，再次更改就没效果了，问题有归结为同一个URL问题，于是再次改了导航页面：

    NavigationService.Navigate(
          new Uri("/View/Tag.xaml?flag="System.DateTime.Now.ToString("hhmmsss")+,
          UriKind.Relative));

在参数后面跟上一个当前时间，这样每次的URL都是不一样的，就成功的欺骗了系统。这样虽然导航没问题了，但是当用户使用系统的返回键时，就会发现无数个相同页面了，解决的方法也很简单，在页面的初始化函数里，根据导航栈的记录判断是不是刷新后的页面：

	foreach (JournalEntry page in App.RootFrame.BackStack.Reverse())
	{
       if (page.Source.ToString().IndexOf("Page1.xaml?flag=") > -1)
       {
           App.RootFrame.RemoveBackEntry();
       }
	}

注意判断条件是`Page1.xaml?flag=`，既能过滤掉正常导航过来的页面，又能找出刷新后的页面，至此，这个变态的做法就大功告成了。
