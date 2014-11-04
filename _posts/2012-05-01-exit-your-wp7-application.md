---
date: "2012-05-01 14:14:22+0800"
layout: post
title: "退出你的WP7应用"
categories: 技术
tags: WPF WP7 Exit 终止程序
---


在WPF程序中，你可以调用“App.Exit()”，你的程序就终止了。但是在 WP7 上面就没那么简单了，因为没有 Exit() 方法。 

如果你抛出一个未被处理的异常，你的程序将被终止 

	Private void Exit()
	{
		Throw new Exception("Exit");
	}
 
但是这个技术将不能通过认证。我这里提到它是想说它曾经是可行的。还有一个方法： 

	Void Exit()
	{
		While (NavigationService.BackStack.Any())
		NavigationService.RemoveBackEntry();
		Base.OnBackKeyPress(new CancelEventArgs());
	}
 
在上面的代码中，我们首先清空回退堆栈。回退堆栈是针对你的程序的。它包含了所有自从你调用 NavigationService 的 Navigate 方法。清空回退堆栈可以保证在你按回退按钮的时候退出你的程序。 

这里的问题是，调用“base.OnBackKeyPress()”不起作用。所以你的退出程序只是清空了回退堆栈，但是没有退出程序。 

下面的代码将解决问题 

	Private void Exit()
	{
		While (NavigationService.BackStack.Any())
		NavigationService.RemoveBackEntry();
		NavigationService.GoBack();
	}
 
在上面的代码中，我们在不能回退的情况下回退。在这种情况下 CanGoBack 属性是false。当 CanGoBack 属性是false的时候调用 GoBack 方法会抛出未被处理的异常。任何未被处理的异常将导致程序退出。 

为什么要这么做？ 
这看起来和直接抛出异常没有什么区别但是我觉得这个方法有价值是因为也许某天没有处理的“throw”语句将不能通过认证。在我看来这个方法有可能成为将来 Exit() 的实现。 

这原本就是个错误！因为WP7的程序就不应该退出，除非是硬件关机。整个框架就没有 Exit 的实现。 

来自微软的解释如下： 

技术认证要求不支持未被处理的异常。 
Exit() 是从别的移动操作系统中延续下来的。 
Exit() 不属于这个平台，我们应该跟随最新的模式。 
所有需要 Exit() 地方都可以用别的方法替代

这里有一个完美的替代方案： 

	Private void Exit()
	{
		While(NavigationService.BackStack.Any())
		NavigationService.RemoveBackEntry();
		
		This.IsHitTestVisible = this.IsEnabled = false;
		
		If(this.ApplicationBar != null)
		Foreach (var item in this.ApplicationBar.Buttons.OfType<ApplicationBarIconButton>())
		Item.IsEnabled = false;
	}
 
在上面的代码中，我们依然清楚了回退堆栈，这是为了保证用户点击回退按钮时无效。然后我们禁用了所有东西 - IsHitTest 阻止了所有触摸事件，然后我们禁用了所有的按钮。 

这个替代方法应该能让你获得 Exit() 同等的效果。 



总结：也许将来我们会有 Exit 方法。但是现在没有。也就是说你的应用不应该退出。
