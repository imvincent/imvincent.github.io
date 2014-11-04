---
date: "2012-04-29 18:57:22+0800"
layout: post
title: "提高WP7应用的用户体验：按压倾斜效果"
categories: 技术
tags: WP7 用户体验 按压倾斜
---


在wp7中，当用户点击一些控件时，如果控件没有任何效果变化会造成用户体验严重下降：用户不知道自己到底有没有点击成功，特别是当这个空间点击后软件需要一个停顿过程以加载数据等操作时，用户可能会以为没有点击成功而多次点击，后果就是…

微软已经为我们准备了一个很好的解决方案：点击时的倾斜3D效果。一般情况下我们只需要将附件中的TiltEffect.cs文件引入自己的项目中，然后修改该文件的命名空间，然后修改要使用倾斜效果的XAML页面：在其头部声明中添加如下语句：

	xmlns:local="clr-namespace:[Namespace]"
	local:TiltEffect.IsTiltEnabled="True"

就可以实现在这个页面中的所有微软觉得需要实现点击效果的控件的倾斜点击效果，如果需要单独定义某个控件的倾斜效果，可以通过如下语句设置该控件的属性：

	local:TiltEffect.SuppressTilt="True"	//取消这个控件的点击效果，False则反之

当然很自然的可能还觉得不够方便，我需要整个程序都使用这种倾斜效果，我是这样实现的，在项目的App.xaml.cs文件中添加如下项目：

	private void Application_Launching(object sender, LaunchingEventArgs e)
	{
	     TiltEffect.SetIsTiltEnabled((App.Current as App).RootFrame, true);
	}
	
这样我们的整个项目都自动加上了倾斜反馈这种很好的动态效果。但问题永远会再度出现：我发现有些控件可能微软觉得不应该有点击效果（比如Border），但我们有时候又有这种特殊的需要，需要它也跟其他空间一样有点击效果，这时候改怎么办呢？其实很简单，在Silverlight中，有一个表示三维效果的类：PlaneProjection，可以通过改变它的RotationY和RotationX两个属性，来变化在平面的角度。这不就是我要的倾斜效果的根源吗，所以我可以将需要实现效果的控件分成平均分成四个区域，判断用户点击的是哪个区域，让该区域下陷，另外一个区域自然就会上浮，跟微软提供的这个效果岂不是一样嘛，试验了一下很好，就是我要的效果，以下是代码：

	//给从canvasGP这个画布控件中取得的所有Border添加三个事件处理程序：分别对应用户开始点击，按下去不松开移动，松开这三个动作
	int num = VisualTreeHelper.GetChildrenCount(canvasGP);  
	for (int i = 0; i < num; i++)
	{
	    Border bo = VisualTreeHelper.GetChild(canvasGP, i) as Border;
	    bo.ManipulationStarted += new EventHandler<ManipulationStartedEventArgs>(border_ManipulationStarted);
	    bo.ManipulationDelta += new EventHandler<ManipulationDeltaEventArgs>(border_ManipulationDelta);
	    bo.ManipulationCompleted += new EventHandler<ManipulationCompletedEventArgs>(border_ManipulationCompleted);
	}
	
	//在事件处理程序中控制这个Border的点击效果，这个Border的大小是170*170
	void border_ManipulationCompleted(object sender, ManipulationCompletedEventArgs e)
	        {
	            PlaneProjection pp = new PlaneProjection();
	            Border bo = sender as Border;
	            pp.RotationX = 0;
	            pp.RotationY = 0;
	            bo.Projection = pp;
	        }
	
	        void border_ManipulationDelta(object sender, ManipulationDeltaEventArgs e)
	        {
	            PlaneProjection pp = new PlaneProjection();
	            Border bo = sender as Border;
	            if (e.ManipulationOrigin.X > 85)
	                pp.RotationY = -10;
	            else
	                pp.RotationY = 10;
	            if (e.ManipulationOrigin.Y > 85)
	                pp.RotationX = 10;
	            else
	                pp.RotationX = -10;
	            bo.Projection = pp;
	        }
	
	        void border_ManipulationStarted(object sender, ManipulationStartedEventArgs e)
	        {
	            PlaneProjection pp = new PlaneProjection();
	            Border bo = sender as Border;
	            if (e.ManipulationOrigin.X > 85)
	                pp.RotationY = -10;
	            else
	                pp.RotationY = 10;
	            if (e.ManipulationOrigin.Y > 85)
	                pp.RotationX = 10;
	            else
	                pp.RotationX = -10;
	            bo.Projection = pp;
	        }
	
这样就完美解决了所有控件的用户反馈倾斜效果，但是实现感觉有点生硬，以后有时间研究一下微软提供的那个TiltEffect.cs文件，看有没有更优雅的解决方案。

微软文件的用法:
文件里默认为两类控件增加了倾斜效果:

	TiltableItems = new List<Type>() { typeof(ButtonBase), typeof(ListBoxItem),};

我们可以其他希望使用倾斜效果的控件添加进来：

	TiltEffect.TiltableItems.Add(typeof(“MyCustomControl”));

微软官方的解释：默认情况下，将常规的 ButtonBase 类和 ListBoxItem 控件指定为可倾斜的项。如果某个控件类型此处不存在，那么倾斜效果将不会应用于任何控件，即使附加了 IsTiltEnabled 依赖项属性并设置为 True 也是如此。您不应该直接修改此代码片断来添加对其他控件的支持。而是应该从您自己的代码来更新该列表。但是，不建议添加其他内置控件，因为应用程序中的倾斜行为将会不一致。

实际使用的效果如微软所言，其他内置控件最好不要添加，会很讨厌，最好只添加自定义控件。

一种解决方案：所有除ButtonBase类的控件如需要实现倾斜效果，则想办法放在ListBox里面。比如我把很多Border放在ListBox里面，定制ListBox的ItemsPanel属性：指定Panel为一个WrapPanel，从而实现了这些Border的倾斜效果。
