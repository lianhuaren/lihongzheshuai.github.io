---
layout: post
title: 从零单排iOS 番外篇 四、《The Swift Programming Language》翻译— 基础 整数和浮点数
date: 2014-06-06 19:39
author: onecoder
comments: true
categories: [iOS基础, swift, swift, 翻译]
---
<blockquote>
	<p>
		此番外篇系列是OneCoder对《The Swift Programming Language》这篇官方文档的翻译和学习。<br />
		最近Google悲剧，而wordpress的一些js需要访问googleapi，所以导致本站目前访问速度可能出现问题。。实在抱歉。。OneCoder会尽快解决。。。</p>
</blockquote>
<h2>
	<br />
	整型(Integers)</h2>
<p>
	&nbsp;</p>
<p>
	整数是不带小数部分的数（OneCoder注：...好废话。），如42, -23。整数可以是有符号数(正数，0或负数)也可以是无符号数(正数或0)。</p>
<p>
	Swift提供8位，16位，32位和64位形式的有符号和无符号整数。这些整数的命名与C类似，即：8位无符号整数对应的类型名称为UInt8，32位有符号整数的类型名称是Int32。与Swift中的所有类型一样，这些整型名称都是首字母大写的。</p>
<h3>
	整型范围(Integer Bounds)</h3>
<p>
	你可以通过每种整数类型中的min和max属性来访问该类型的最小和最大值:</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
    let minValue = UInt8.min // minValue is equal to 0, and is of type UInt8
    let maxValue = UInt8.max // maxValue is equal to 255, and is of type UInt8
</pre>
<p>
	这些属性的值属于相应大小的数字类型(如上面例子中UInt8)，因此可以用在含有相同类型的其他值的表达式中。</p>
<h3>
	整型(Int)</h3>
<p>
	在大多数情况下，你无需在代码中指定整型的大小。Swift提供了一种额外的整型Int，该类型的大小与当前平台的本地字大小一致：</p>
<blockquote>
	<p>
		* 在32位平台上， Int与Int32的大小一致。<br />
		* 在64位平台上， Int与Int64的大小一致。</p>
</blockquote>
<p>
	在你的代码中，除非你需要指定整型的大小，否则你都可以使用Int来表示整型。这有助于代码的一致性和互通性。即使在32位的平台上，Int也可以存储范围在-2,147,483,648 到2,147,483,647之间的整数，对于绝大多数的整数来说已足够大。</p>
<h3>
	无符号整型(Unit)</h3>
<p>
	Swift也提供了一个无符号整型UInt，同样与当前平台的本地字一致：</p>
<blockquote>
	<p>
		* 在32位平台上， UInt与UInt32的大小一致。<br />
		* 在64位平台上， UInt与UInt64的大小一致。</p>
</blockquote>
<blockquote>
	<p>
		注意：</p>
	<p>
		只有当你确实需要一个与平台本地字大小一致的无符号整型的时候才使用UInt类型。如果不是这样，推荐使用Int，哪怕已知保存的值是非负的。坚持使用Int类型对代码的互通性很有帮助，可以避免不同数字类型之间的转换，同时还符合整型推演。（将在类型安全和类型推断(Type Safety and Type Inference. )部分介绍）。</p>
</blockquote>
<h2>
	浮点型数字(Floating-Point Numbers)</h2>
<p>
	浮点型数字是包含小数部分的数字，如3.14159, 0.1 和 -273.15。<br />
	浮点型可以表示值的范围比整型大的多，因此可以存储比Int类型大的多或小的多的值。Swift提供了两种有符号的浮点型数字类型：</p>
<blockquote>
	<p>
		* Double，表示64位浮点型数字。当浮点型值非常大或者要求特别精确时使用。<br />
		* Float，表示32位浮点型数字。当浮点型值不需要64位精度时使用。</p>
</blockquote>
<blockquote>
	<p>
		注意：</p>
	<p>
		Double 类型的精度至少为15位有效数字，而Float类型的精度只有6位有效数字。合理的浮点型选择取决于你代码里值的性质和范围。</p>
</blockquote>
<p>
	未完待续&hellip;&hellip;&hellip;&hellip;OneCoder与您一起&hellip;&hellip;下一节：<a href="http://www.coderli.com/30old-fromzero-alone-swift-programming-language-translate-5">类型安全和类型推断</a></p>

