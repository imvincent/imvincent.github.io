---
date: "2014-07-26 16:33:33+0800"
layout: post
title: "Using Microsoft Advertising SDK"
categories: 技术
tags: Advertising Micorsoft 
---

## Install SDK:
   
Since this sdk is included in vs2013 already, when you have vs2013 installed, this step is done already: you could check it using vs2013 Extension and Updates manger.

## Walkthroughs:
   
1. In Package.appxmanifest, ensure that the internet client check box has been checked in the Capabilities tab.
2. Add sdk reference in your project: Microsoft Advertising SDK for Windows 8.1.
3. Now you could use the sdk in your xaml code like below or use it in c# code behind as ohter UIElement.

```xml
<Page
	xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation" 
	xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml" 
	xmlns:local="using:My_Windows_8._1_Ad_Funded_XAML_App" 
	xmlns:d="http://schemas.microsoft.com/expression/blend/2008" 
	xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006" 
	xmlns:UI="using:Microsoft.Advertising.WinRT.UI" 
	x:Class="My_Windows_8._1_Ad_Funded_XAML_App.MainPage" 
	mc:Ignorable="d">
	<Grid Background="{StaticResource ApplicationPageBackgroundThemeBrush}"> 
	      <UI:AdControl ApplicationId="d25517cb-12d4-4699-8bdc-52040c712cab" 
	      				AdUnitId="10043107" 
	      				HorizontalAlignment="Left" 
	                    Height="250" 
	                    VerticalAlignment="Top" 
	                    Width="250"/>
	</Grid>
</Page>
```

4. Change the AdUnitId property listed in the TestMode topic (http://msdn.microsoft.com/en-us/library/advertising-windows-test-mode-values(v=msads.10).aspx) can give you diffenent ad types. Note that the width and height property can only be set match or greater than the size of current ad type, or the add may not displayed.
5. Use xml or other config methord to change property between Debug and release will be convenient.
6. Go to ms pubCenter to register your ApplicationID and AdUnitIDs for your release version.
7. When you submit your app to windows store, you should know this Windows Store Requirements for the Advertising SDK:
  - Your app must have an Age rating of at least 12+ in the Windows Store to receive Ads.
  - Your app must provide notice and obtain consent for user, for more information, you could visit msdn page: http://msdn.microsoft.com/en-US/library/advertising-windows-store-submission%28v=msads.10%29.aspx