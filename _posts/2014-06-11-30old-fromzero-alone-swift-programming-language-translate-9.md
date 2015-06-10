---
layout: post
title: 从零单排iOS 番外篇 九、《The Swift Programming Language》翻译 基础 布尔值
date: 2014-06-11 13:24
author: onecoder
comments: true
categories: [swift, swift, 官方文档, 翻译]
---
<blockquote>
	<p>
		此番外篇系列是OneCoder对《The Swift Programming Language》这篇官方文档的翻译和学习。<br />
		还是喝粥&hellip;&hellip;肚子又坏了&hellip;&hellip;次奥&hellip;&hellip;</p>
</blockquote>
<hr />
<h3>
	布尔值(Booleans)</h3>
<p>
	Swift拥有基础的布尔类型: Bool。布尔类型用于逻辑判断，因为他们只可能是true(真)或者false(假)。Swift提供了两个布尔常量值，true和false:</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
   let orangesAreOrange = true
    let turnipsAreDelicious = false
</pre>
<p>
	由于常量orangesAreOrange和turnipsAreDelicious的初始值为布尔型的字面值，因此他们的类型被推断为Bool型。如上面介绍的Int和Double一样，如果你在声明常量或变量的时候设置了true或false的初始值，那么就不必声明他们为Bool类型。在用一个其他的已知类型的值初始化一个常量或变量的时候，类型推断机制使得Swfit的代码更加简洁易读。</p>
<p>
	布尔值在你使用如if等条件语句的时候尤为有用:</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
if turnipsAreDelicious {
println(&quot;Mmm, tasty turnips!&quot;)
} else {
println(&quot;Eww, turnips are horrible.&quot;)
}
// prints &quot;Eww, turnips are horrible.&quot;
</pre>
<p>
	if等条件语句将会在控制流程(Control Flow)部分详细介绍。</p>
<p>
	Swfit的类型安全机制避免了非布尔型的值误当Bool值使用。下面例子会报编译错误:<br />
	&nbsp;</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
   let i = 1
    if i {
    // this example will not compile, and will report an error
    }
</pre>
<p>
	不过，下面的代码是合法的：</p>
<br />
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
    let i = 1
    if i == 1 {
    // this example will compile successfully
    }
</pre>
<p>
	i==1比较的结果是Bool型的值，因此第二个例子可以通过类型检查。类似i==1这样的比较语句将会在基本操作符(Basic Operators)部分详细介绍。</p>
<p>
	如Swift中其他类型安全的例子一样，这种处理可以避免意外错误并且可以保证局部代码的目的总是清晰的。</p>
<p>
	未完待续，请期待&hellip;&hellip;下一节：<a href="http://www.coderli.com/30old-fromzero-alone-swift-programming-language-translate-10" target="_blank">元组（Tuples）</a>&hellip;&hellip;<br />
	&nbsp;</p>

