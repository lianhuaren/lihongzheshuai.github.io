---
layout: post
title: 白话Java - Double.NaN
date: 2012-06-28 12:49
author: onecoder
comments: true
categories: [Java, Java基础, NaN, 白话]
---
<span style="font-family: Tahoma; font-size: 14px; ">今天处理一个关于Double.NaN的异常。</span><br />
<pre style="color:#A30C0C">
Unknown column &#39;NaN&#39; in &#39;field list&#39;
</pre>
<div style="font-family: Tahoma; font-size: 14px; ">
	NaN&nbsp;= Not a Number。就是不是数字。我们用0/0.0打印出来的结果就是Nan。<br />
	<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
Double d = 0/0.0;
System.out.println(d);
</pre>
</div>
<span style="font-family: Tahoma; font-size: 14px; ">JDK的Double类里，提供的NaN的实现是：</span><br />
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
public static final double NaN = 0.0d / 0.0;
</pre>
<span style="font-family: Tahoma; font-size: 14px; ">如果是非零的数除以0.0，得出的是：Infinity。正数除以0.0，就是正Infinity，负数就是-Infinity。</span><span style="font-family: Tahoma; font-size: 14px; ">JDK里给出样例是：</span><br />
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
&nbsp;&nbsp;&nbsp;&nbsp;/**
     * A constant holding the positive infinity of type
     * {@code double}. It is equal to the value returned by
     * {@code Double.longBitsToDouble(0x7ff0000000000000L)}.
     */
    public static final double POSITIVE_INFINITY = 1.0 / 0.0;

    /**
     * A constant holding the negative infinity of type
     * {@code double}. It is equal to the value returned by
     * {@code Double.longBitsToDouble(0xfff0000000000000L)}.
     */
    public static final double NEGATIVE_INFINITY = -1.0 / 0.0;
</pre>
<span style="font-family: Tahoma; font-size: 14px; ">用控制台啊打印的结果是：</span><br />
<pre>
<span style="color:#800000;">Infinity/-Infinity
</span></pre>
<div style="font-family: Tahoma; font-size: 14px; ">
	既然，NaN不是一个数，那他跟任何数比较都不相等，即使跟他自己比较。</div>
<div style="font-family: Tahoma; font-size: 14px; ">
	<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
System.out.println(Double.NaN == Double.NaN);//输出:false
</pre>
</div>
<div style="font-family: Tahoma; font-size: 14px; ">
	说点通俗的，什么情况下出现NaN？其实这就是数学老师教的那些，没有意义的计算的结果。比如0/0没有意义。老师还教过什么？对啊，负数的平方根啊。</div>
<div style="font-family: Tahoma; font-size: 14px; ">
	所以，用Math.sqrt(-n)，算出来的结果，也是NaN。<br />
	<br />
	不过有一点，我也不能很好解释的就是(int) Double.NaN = 0。。这个，就先这样吧。</div>

