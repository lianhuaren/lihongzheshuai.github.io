---
layout: post
title: 从零单排iOS 番外篇 六、《The Swift Programming Language》翻译 基础 数字字面值
date: 2014-06-09 00:03
author: onecoder
comments: true
categories: [swift, swift, 官方文档, 翻译]
---
<blockquote>
	<p>
		此番外篇系列是OneCoder对《The Swift Programming Language》这篇官方文档的翻译和学习。<br />
		今天嘴里还是肿的，腊肠一样，真是遭罪&hellip;&hellip;</p>
</blockquote>
<h3>
	数字字面值(Numeric Literals)(OneCoder注：其实就是数字的各种写法)</h3>
<p>
	整数有如下几种表示方法：</p>
<blockquote>
	<p>
		&nbsp; &nbsp;十进制数字，没有前缀<br />
		&nbsp; &nbsp;二进制数字，以0b开头<br />
		&nbsp; &nbsp;八进制数字，以0o开头<br />
		&nbsp; &nbsp;十六进制数字，以0x开头。</p>
</blockquote>
<p>
	以下所有整数的值，在十进制下都是17:</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
let decimalInteger = 17
let binaryInteger = 0b10001       // 17 in binary notation
let octalInteger = 0o21           // 17 in octal notation
let hexadecimalInteger = 0x11     // 17 in hexadecimal notation
</pre>
<p>
	浮点数的表示方法可以是无前缀的十进制小数，或者是是0x开头的十六进制数字。浮点数的小数点两侧必须都有数字(十六进制的也可)。你也可以用大写或小写的e来表示十进制浮点数的指数，用大写或小写的p来表示十六进制浮点数的指数。</p>
<p>
	对于十进制以exp为指数的数字来说，相当于基数乘以10的exp次幂 :</p>
<blockquote>
	<p>
		1.25e2 相当于1.25 &times; 10^2, 或 125.0.<br />
		1.25e-2 相当于 1.25 &times; 10^-2, 或 0.0125.</p>
</blockquote>
<p>
	对于十六进制以exp为指数的数字来说，相当于基础乘以2的exp次幂：</p>
<blockquote>
	<p>
		0xFp2 相当于15 &times; 2^2, 或 60.0.<br />
		0xFp-2 相当于 15 &times; 2^-2, 或 3.75.</p>
</blockquote>
<p>
	下面所有的浮点型表示的值都是12.1875:</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
let decimalDouble = 12.1875
let exponentDouble = 1.21875e1
let hexadecimalDouble = 0xC.3p0//(OneCoder注：特意查了一下计算方法。C就是12了，小数部分3/16 * 2^0 就是1875了)
</pre>
<p>
	数字表示也可以有特殊的格式方便阅读。整型和浮点型都可以添加额外的0和下划线以方便阅读。下面的每种格式都不会到字面的值：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
let paddedDouble = 000123.456
let oneMillion = 1_000_000
let justOverOneMillion = 1_000_000.000_000_1
</pre>
<p>
	嘴好疼&hellip;&hellip;未完待续&hellip;&hellip;下一节：<a href="http://www.coderli.com/30old-fromzero-alone-swift-programming-language-translate-7" target="_blank">数字转换</a></p>

