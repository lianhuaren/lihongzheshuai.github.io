---
layout: post
title: 从零单排iOS 番外篇 七、《The Swift Programming Language》翻译 基础 数字类型转换
date: 2014-06-09 23:00
author: onecoder
comments: true
categories: [iOS教程, swift, swift, 翻译]
---
<blockquote>
	<p>
		此番外篇系列是OneCoder对《The Swift Programming Language》这篇官方文档的翻译和学习。<br />
		中午又是一碗清粥&hellip;&hellip;已减肥10斤&hellip;&hellip;&hellip;&hellip;痛苦&hellip;&hellip;</p>
</blockquote>
<hr />
<p>
	&nbsp;</p>
<h3>
	数字类型转换(Numeric Type Conversion)</h3>
<p>
	对所有常见用途的整型常量和变量使用Int类型，哪怕你知道他们是非负的。在每个解决方案中使用默认的整型类型意味着你的代码立即拥有了互通性，并且将符合整型字面值的类型推断。</p>
<p>
	只有当你确定手里的任务需要其他的整型的时候，再去使用他们，如，精确的数据大小来自外部来源，或是出于性能、内存使用或其他必要的优化的考虑。在这些情况下，使用精确大小的数据类型可以帮助你处理值的意外溢出，同时也隐含的记录下了所使用数据的性质。</p>
<p>
	<strong><em>整型转换(Integer Conversion)</em></strong></p>
<p>
	不同的数据类型可以保存的整型常量或变量的范围是不同的。Int8型的常量或变量可以保存范围从-128到127的值，UInt型的常量或变量可以保存从0至255的值。在指定大小的整常量或变量中保存不合适的数字会在编译时报错:</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
let cannotBeNegative: UInt8 = -1
// UInt8 cannot store negative numbers, and so this will report an error
let tooBig: Int8 = Int8.max + 1
// Int8 cannot store a number larger than its maximum value,
// and so this will also report an error
</pre>
<p>
	由于每种数据类型可以保存不同范围的值，你必须分情况的选择数据转换方式。这种选择方式可以避免隐含的转换错误，同时可以使代码中类型转换的目标更明确。</p>
<p>
	为了将一个指定的数字类型转换成另外一个，你需要用现有值初始化一个新的目标类型的数字。下面的例子中，常量twoThousand是UInt16类型的，而常量one是UInt8类型的。它们无法直接相加，因为他们是不同类型的。所以，例子中调用UInt16(one)来使用one的值创建一个新的UInt16类型的常量，然后用新值来代替原来的值：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
let twoThousand: UInt16 = 2_000
let one: UInt8 = 1
let twoThousandAndOne = twoThousand + UInt16(one)
</pre>
<p>
	由于现在加号两边的值都是UInt16类型的，所以可以相加。输出常量(twoThousandAndOne)被推断成UInt16类型的，因为它是两个UInt16值的和。</p>
<p>
	SomeType(ofInitialValue) 某类型(初始值）是默认调用Swift类型的初始化器并传递初始值的方式。基于这种场景，UInt16有一个可以接受UInt8类型值的初始化器，因此该初始化器可以用来从一个已有的UInt8的值构造一个新的UInt16的值。不过，你不可能传递任何类型的值，而必须是UInt16类型提供初始化方法的值的类型。我们将在扩展(Extensions)部分介绍如何扩展已有类型的构造器来接受新的类型（包括自定义类型）。</p>
<p>
	<strong><em>整型和浮点型转换（Integer and Floating-Point Conversion）</em></strong></p>
<p>
	<br />
	整型和浮点型数字之间的转换必须是显式的：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
let three = 3
let pointOneFourOneFiveNine = 0.14159
let pi = Double(three) + pointOneFourOneFiveNine
// pi equals 3.14159, and is inferred to be of type Double
</pre>
<p>
	<br />
	这里，常量three的值用于构造一个新的Double类型的值，这样加号两边的值的类型变得相同。如果没有这个转换，是无法进行加法的。</p>
<p>
	从浮点型到整型的转换也是可以的，整型可以通过Double或Float类型的值初始化：</p>
<p>
	let integerPi = Int(pi)<br />
	// integerPi equals 3, and is inferred to be of type Int</p>
<p>
	<br />
	当用上面的方式初始化一个新的整型的时候，浮点型的值总会被截断。也就是说4.75会变成4，-3.9变成-3。</p>
<blockquote>
	<p>
		<br />
		注意：</p>
	<p>
		结合常量或变量的规则与数字字面值的规则是不同的。字面值3可以直接与0.14159相加，因为数字字面值没有明确的类型声明。他们的类型是在编译器评估的时候推断出来的。</p>
</blockquote>
<p>
	&nbsp;</p>
<p>
	嘴有好转，未完待续&hellip;&hellip;&hellip;&hellip;下一节：<a href="http://www.coderli.com/30old-fromzero-alone-swift-programming-language-translate-8" target="_blank">类型别名</a></p>

