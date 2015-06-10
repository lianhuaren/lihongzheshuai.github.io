---
layout: post
title: 30岁码农从零单排iOS 第一季 8. 变量和数据类型（下）
date: 2014-06-12 22:33
author: onecoder
comments: true
categories: [iOS, iOS基础, iOS教程, objective-c, objective-c基础]
---
<blockquote>
	<p>
		第一季单排什么？是OneCoder学习《Objective-C程序设计》(第4版)的经过和总结。</p>
</blockquote>
<p>
	接昨天的部分&hellip;&hellip;继续撸&hellip;&hellip;OneCoder决定改一下风格，采用&ldquo;边打边录边解说&rdquo;的方式，即直接上代码，理论主要放在注释里的方式～这也是OneCoder比较习惯的方式～。～</p>
<p>
	好了，开撸。首先是关于枚举的:</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
/* 构造一个枚举类型, 其中weekday是枚举名，大括号里是三个可选值
         枚举的值，实际是一个int，默认从0开始，我们可以手动赋值，覆盖默认。
         后面的值始终保持+1.
         */
        enum weekday {Monday = 1, Tuesday, WendnesDay};
        /*
         使用枚举的例子
         */
        // 构造一个weekday枚举类型的变量，并赋值
        enum weekday day = Tuesday;
        // 枚举还可以做加法，不会报错。当然，也可以显示强转：day = (enum weekday)(day + 1);
        day = day + 1;
        // 在switch语句中使用枚举, 同时输出枚举的值。
        switch(day){
            case Monday:
                NSLog(@&quot;今天是星期: %i&quot;, day);
                break;
            case Tuesday:
                NSLog(@&quot;今天是星期: %i&quot;, day);
                break;
            case WendnesDay:
                NSLog(@&quot;今天是星期: %i&quot;, day);
                break;
        }
</pre>
<p>
	感觉注释里已经够了，没有什么需要补充的了，直接开撸类型定义的代码，在代码中会解释什么是类型定义：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
        /*
         类型定义练习，类型定义可以理解为给一个已知的类型起个更有语义的别名。
         以后，用新的名字来代替原来的名字，改名～～
         */
        // 将原来的枚举类型weekday改名为OneCoderDay
        typedef enum weekday OneCoderDay;
        //使用之
        OneCoderDay oDay = WendnesDay;
        NSLog(@&quot;今天是星期%i&quot;, oDay);
</pre>
<p>
	通过撸代码来学习感觉就是酸爽。关于数据类型转换，一篇很长的文字。总结一下，就是：</p>
<blockquote>
	<p>
		&nbsp;&nbsp; * 这里说的数据类型转换是指发生在数学表达式中的转换。根据操作符两边的数字类型进行转换。<br />
		&nbsp;&nbsp; * OneCoder归纳了一下原则就是对于出现比int范围大的类型(long double, double, float, long long int, long int, )，往出现的最大的转。<br />
		&nbsp;&nbsp; * 有一个比Int小的(Bool、char、short int, bit field)都转为int。<br />
		&nbsp;&nbsp; * 全是int还是int</p>
</blockquote>
<p>
	以上规则针对有符号数而言。</p>
<p>
	举例：<br />
	f * i + l /s</p>
<p>
	f * i 转成float， l/s&nbsp; ，s -&gt; int，整体 -&gt; l，然后变成f + l，转成f。</p>
<p>
	最后是位运算，就是按位操作数，直接上练习代码。</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
        /*
         位运算练习
         */
        //
        int a4 = 0b00000100;
        int a7 = 0b00000111;
        // 按位与 &amp; (都是1，才是1)
        int result = a4 &amp; a7;//0b00000100 = 4;
        NSLog(@&quot;%i &amp; %i = %i&quot;, a4, a7, result);
        // 按位或 | (有一个1，就是1)
        result = a4 | a7;//0b0000 0111 = 7;
        NSLog(@&quot;%i | %i = %i&quot;, a4, a7, result);
        // 按位异或 ^ (有1，但是不都是1的时候，值为1)
        result = a4 ^ a7;//0b0000 0011 = 3;
        NSLog(@&quot;%i ^ %i = %i&quot;, a4, a7, result);
        // 求反 ~ (0变1，1变0)
        result = ~a4;// 0b 1111 1011 = -5;
        NSLog(@&quot;~ %i = %i&quot;, a4, result);
        // 向左位移 &lt;&lt;
        result = a4 &lt;&lt; 2;//0b0001 0000 = 16;
        NSLog(@&quot;%i &lt;&lt; 2 = %i&quot;, a4, result);
        // 向右位移 &gt;&gt; (在不同的计算机上，对于有符号数向右位移可能会有不同的结果。因为可能补0或者1)
        result = a4 &gt;&gt; 2;//0b0000 0001 = 1;
        NSLog(@&quot;%i &gt;&gt; 2 = %i&quot;, a4, result);

</pre>
<p>
	差不多就是这样了。。继续读书去了。对了，世界杯开了～～</p>

