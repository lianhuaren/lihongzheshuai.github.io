---
layout: post
title: 从零单排iOS 番外篇 五、《The Swift Programming Language》翻译 基础 类型安全和类型推断
date: 2014-06-07 16:20
author: onecoder
comments: true
categories: [iOS基础, iOS教程, swift, swift, 翻译]
---
<blockquote>
	<p>
		此番外篇系列是OneCoder对《The Swift Programming Language》这篇官方文档的翻译和学习。<br />
		今天切了个嘴里的泡，突出一个疼字&hellip;&hellip;&hellip;&hellip;</p>
</blockquote>
<hr />
<h3>
	类型安全和类型推断(类型安全和类型推断)</h3>
<p>
	Swift是类型安全的语言。类型安全的语言可以让你清楚的知道你的代码中可以使用的值的类型。如果你代码里需要的是String类型，你就不会错误的传递给他Int类型。</p>
<p>
	由于Swift是类型安全的，所以它会在编译期检查你的代码，并会以错误信息的方式标记出任何类型不匹配的地方。这可以帮助你在开发期尽早的发现和修复错误。</p>
<p>
	在你处理不同类型的值的时候，类型检查可以帮助你避免错误。然而，这并不意味着你必须指定你声明的每个常量和变量的类型。如果你没有指定值的类型，Swift会使用类型推断机制来推断出合理的值的类型。类型推断机制使得编译器可以在编译代码的时候，通过检查你提供的值，来自动推断出表达式中的值的类型。</p>
<p>
	正是借助于类型推断机制，Swift对类型声明的需要远小于C或Objective-C等其他语言。这通常需要你在声明常量或变量的时候赋予字面值(literal value)。（字面值是指在代码中直接出现的值，如下面例子中的42和3.14159。）</p>
<p>
	例如，如果你直接给某常量赋值42而没有指定其类型，Swift会推测出你期望该常量是Int型的，因为你给定的初始化值看起来像整数：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
let meaningOfLife = 42
// meaningOfLife is inferred to be of type Int
</pre>
<p>
	同样的，如果给你定了一个浮点型的字面值，Swift会推测认为你想要的是Double类型:</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
let pi = 3.14159
// pi is inferred to be of type Double
</pre>
<p>
	在推测浮点型类型的时候，Swift总会使用Double而不是Float。</p>
<p>
	如果你在同一个表达式中组合使用整型和浮点型的值，那么将会推测为Double类型：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
let anotherPi = 3 + 0.14159
// anotherPi is also inferred to be of type Double
</pre>
<p>
	字面值3没有明确的类型，因此推测输出类型为Double依赖于加法表达式中的浮点型字面值。</p>
<p>
	嘴好疼,未完待续&hellip;&hellip;下一节：<a href="http://www.coderli.com/30old-fromzero-alone-swift-programming-language-translate-6" target="_blank">数字字面值</a></p>

