---
layout: post
title: 30岁码农从零单排iOS 番外篇 一、《The Swift Programming Language》翻译— 基础 简介
date: 2014-06-03 14:17
author: onecoder
comments: true
categories: [iOS, os x, swift, swift, 翻译]
---
<p>
	端午休假归来，惊闻大苹果发布了新的开发语言，我勒个擦。赶紧擦干净屁股开始学习。从今天开始学习并翻译，Language Guide部分。</p>
<blockquote>
	<p>
		什么是从零单排？DOTA大神们曾经流行的视频系列。不同的是，我是真正的从&ldquo;0&rdquo;单人自学，DOTA大神们是卖萌。<br />
		为什么从零单排？ 就想看看自己能不能坚持做好一件事情。<br />
		为什么是iOS？听起来高大上。移动互联时代，分杯翔也好。<br />
		为什么是30岁码农？因为30岁了，还是码农。<br />
		<span style="color:#ff0000;">番外篇</span>是什么？是<a href="http://www.coderli.com">OneCoder</a>对《The Swift Programming Language》这篇官方文档的翻译和学习。</p>
</blockquote>
<hr />
<h2>
	基础<br />
	&nbsp;&nbsp;&nbsp;&nbsp;</h2>
<p>
	&nbsp;&nbsp;&nbsp;&nbsp; Swift是一门新的开发iOS和OS X应用的编程语言。不过，Swift的很多方面都和你熟悉的C和Objective-C的开发类似。</p>
<p>
	&nbsp;&nbsp;&nbsp;&nbsp; 对于C和Objective-C中的基本数据类型，Swift提供了自己的对应版本，包括整型Int， 浮点型Double和Float，布尔型Bool 和文本型String。Swift也提供了两个强大的集合数据类型，Array和Dictionary，这两个类型将会在集合类型部分详细介绍。</p>
<p>
	&nbsp;&nbsp;&nbsp;&nbsp; 像C一样，Swift用指定名称的变量来保存和指向值。Swift也大量的使用了值不可修改的变量，被称之为常量。Swift中常量比C中的常量作用大的多。在Swift中，常量的使用使代码更安全，并且在处理不需要改变的值的时候会让代码更整洁。</p>
<p>
	&nbsp;&nbsp;&nbsp;&nbsp; 除了熟知的类型，Swift还提供了Objective-C中没有的类型。例如，tuples，该类型可以用来创建和传递一组值。Tuples可以使你的函数以一个单独的组合值的方式返回多个值。</p>
<p>
	&nbsp;&nbsp;&nbsp;&nbsp; Swift还提供了适配类型(optional type)，该类型用来处理某值的缺失。适配类型要么代表：&ldquo;他有一个值，他的值是x&rdquo;，要么代表&ldquo;他没有值&rdquo;。适配类型类似于在Objective-C中使用带指针的nil，不过适配类型可以应用于任何类型，而不仅仅是类。适配类型比Objective-C中的nil指针更安全和强大，并且是许多Swift中最重要特性的核心。</p>
<p>
	&nbsp;&nbsp;&nbsp;&nbsp; 适配类型是证明Swift是类型安全语言的一个例证。Swift会帮助你清楚明白你的代码可正常运转的值的类型。例如，如果你的代码需要String类型，类型安全机制可以使你避免错误的传递一个Int类型的值。这会帮助你在开发中尽早的捕获并修复错误。</p>
<p>
	&nbsp;&nbsp;&nbsp;&nbsp; 未完待续&hellip;&hellip;&hellip;&hellip;下一节: <a href="http://www.coderli.com/30old-fromzero-alone-swift-programming-language-translate-2">基础 常量和变量</a><br />
	&nbsp;</p>

