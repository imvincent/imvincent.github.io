---
date: "2012-05-05 16:57:22+0800"
layout: post
title: "解决WP7中对同一个url的http请求获取不到最新结果的问题"
categories: 技术
tags: WP7 WebClient Guid
---

最近在wp7项目中需要调用 http-get 操作从网络上获取返回的xml结果
实例代码如下

	WebClient client = new WebClient( );
	client. DownloadStringCompleted += (s, e) =>
	{
	   //处理返回的结果
	};
	client. DownloadStringAsync(new Uri(url, UriKind. Absolute));
	
一般说来，这个url肯定都是附带了查询字符串参数的完整url 
比如 http://www.xxx.com?action=xx&id=x 这样的。 

但是我们发现如果你在wp7项目中连续两次调用这个 webclient 来获取消息，则第二次实际上是没有网络请求的，关键就在于wp7已经内建了这个缓存结果，导致第二次根本不去服务器拉取数据。  

所以要解决这个办法的临时方案就是在 查询字符串上再加一个参数来证明连续两次的网络请求不是完全一样的。 
我们可以加上这样一个 名值对参数 

	url = string.Format("http://www.xxx.com?action=xx&id=x&guid={0}", 
          Guid. NewGuid( )); 
	
然后再次测试连续请求 就发现完美解决了这个问题。。。。 ,  

由于 Guid.NewGuid() 产生相同结果的可能性是 1/ 2^128 ，所以各位就不用担心这个 guid 可能会重复的问题了