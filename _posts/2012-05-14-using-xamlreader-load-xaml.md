---
date: "2012-05-14 16:57:22+0800"
layout: post
title: "使用XamlReader在后台代码中动态加载xaml"
categories: 技术	
tags: WP7 XamlReader 动态加载
---

	T LoadXAMLAndGetShape<T>(string url)
        {
            string xaml = string.Empty;

            StreamResourceInfo sri = Application.GetResourceStream(
										new Uri(url, UriKind.Relative));

            using (StreamReader sr = new StreamReader(sri.Stream))
            {
                xaml = sr.ReadToEnd();
            }

            T shape = (T)XamlReader.Load(xaml);

            return shape;
        }


调用方式：

	myGrid = LoadXAMLAndGetShape<Grid>(
			"/wp_trade;component/MetroControls/fundsHoldPositionView.xaml");
