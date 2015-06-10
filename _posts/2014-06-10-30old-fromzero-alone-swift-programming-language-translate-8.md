---
layout: post
title: 从零单排iOS 番外篇 八、《The Swift Programming Language》翻译 基础 类型别名
date: 2014-06-10 13:34
author: onecoder
comments: true
categories: [swift, swift, 官方文档, 翻译]
---
<blockquote>
	<p>
		此番外篇系列是OneCoder对《The Swift Programming Language》这篇官方文档的翻译和学习。<br />
		今天有好转，但是翻译较短&hellip;&hellip;勿怪&hellip;&hellip;</p>
</blockquote>
<hr />
<p>
	<strong><em>类型别名(Type Aliases)</em></strong><br />
	&nbsp;</p>
<p>
	类型别名是给一个已经存在的类型定义一个可替换的名字。你可以使用关键字typealias进行类型别名定义。</p>
<p>
	在你想用一个语义上更加合适的名字来使用一个已有的类型的时候，类型别名非常有用，例如：在使用外部代码里指定大小的数据的时候：</p>
<br />
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
typealias AudioSample = UInt16
</pre>
<p>
	当你定义了类型别名，你就可以任意地方像使用原名一样使用别名：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
    var maxAmplitudeFound = AudioSample.min
    // maxAmplitudeFound is now 0
</pre>
<p>
	这里，AudioSample定义为 UInt16的别名。由于它是别名，所以调用AudioSample.min就相当于调用UInt16.min，即给变量maxAmplitudeFound赋了初值0。</p>
<hr />
<p>
	OneCoder承认，作为男人，今天确实短了一点，不过午休翻译。再加上下面的部分跟这个关系不是特别密切，不想强行弄一起，所以&hellip;&hellip;&hellip;&hellip;未完待续&hellip;&hellip;下一节: <a href="http://www.coderli.com/30old-fromzero-alone-swift-programming-language-translate-9">布尔型</a></p>

