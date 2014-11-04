---
date: "2012-05-05 18:57:22+0800"
layout: post
title: "C#中的四舍五入"
categories: 技术
tags: C# rounding 四舍五入
---


从统计学的角度,"四舍六入五成双"比"四舍五入"要科学,它使舍入后的结果有的变大,有的变小,更平均.而不是像四舍五入那样逢五就入,导致结果偏向大数.

例如: `1.15+1.25+1.35+1.45=5.2`,
若按四舍五入取一位小数计算: `1.2+1.3+1.4+1.5=5.4`
按"四舍六入五成双"计算: `1.2+1.2+1.4+1.4=5.2`
舍入后的结果更能反映实际结果。

四舍六入五成双：

	Math.Round(43.5,0) --44.0
	Math.Round(42.5,0) --42.0

解决方法如下
 1.只要求保留N位不四舍5入 

    float f = 0.55555f;
    int i =(int)(f * 100);           
    f = (float)(i*1.0)/100;

 2.保留N位,四舍五入  

    decimal d= decimal.Round(decimal.Parse("0.55555"),2);
  
 3.保留N位四舍五入

    double dbdata = 0.55555;
    string str1 = bdata.ToString("F2");//fN 保留N位，四舍五入

 4.保留N位四舍五入
  
    string result =String.Format("{0:N2}", 0.55555);//2位 
    string result = String.Format("{0:N3}", 0.55555);//3位

 5.保留N位四舍五入

    double s=0.55555;         
    result=s.ToString("#0.00");//点后面几个0就保留几位 
