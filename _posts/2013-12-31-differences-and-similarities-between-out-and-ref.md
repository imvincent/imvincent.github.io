---
date: "2012-09-10 14:14:22+0800"
layout: post
title: "C#中Out和Ref的异同"
categories: 技术
tags: C# 参数 out ref 
---

ref是传递参数的地址，out是返回值，两者有一定的相同之处，不过也有不同点。

使用ref前必须对变量赋值，out不用。

out的函数会清空变量，即使变量已经赋值也不行，退出函数时所有out引用的变量都要赋值，ref引用的可以修改，也可以不修改。 

区别可以参看下面的代码：

	public class OutRef 
	{ 
		static void outTest(out int x, out int y) 
	    { 
	        //离开这个函数前，必须对x和y赋值，否则会报错。  
	        //y = x;  
	        //上面这行会报错，因为使用了out后，x和y都清空了，需要重新赋值，即使调用函数前赋过值也不行  
	        x = 1; 
	        y = 2; 
	    } 
	    static void refTest(ref int x, ref int y) 
	    { 
	        x = 1; 
	        y = x; 
	    } 
	    public static void OutRefTest() 
	    { 
	        //out test 
	        int a, b; 
	        //out使用前，变量可以不赋值 
	        outTest(out a, out b); 
	        String outMsg = String.Format("a is {0}, b is {1} ", a, b); 
	        int c = 11, d = 22; 
	        outTest(out c, out d); 
	        String outMsg2 = String.Format("a is {0}, b is {1} ", a, b); 
	 
	        //ref test 
	        int m, n; 
	        //refTest(ref m, ref n);  
	        //上面这行会出错，ref使用前，变量必须赋值 
	 
	        int o = 11, p = 22; 
	        refTest(ref o, ref p); 
	        String refMsg = String.Format("a is {0}, b is {1} ", o, p); 
	    } 
	}
