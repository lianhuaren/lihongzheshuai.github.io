---
layout: post
title: 从零单排iOS 番外篇 三、《The Swift Programming Language》翻译— 基础 注释和分号
date: 2014-06-05 13:58
author: onecoder
comments: true
categories: [iOS, swift, swift, The Swift Programming Language, 翻译]
---
<blockquote>
	<p>
		什么是从零单排？DOTA大神们曾经流行的视频系列。不同的是，我是真正的从&ldquo;0&rdquo;单人自学，DOTA大神们是卖萌。<br />
		为什么从零单排？ 就想看看自己能不能坚持做好一件事情。<br />
		为什么是iOS？听起来高大上。移动互联时代，分杯翔也好。<br />
		为什么是30岁码农？因为30岁了，还是码农。<br />
		番外篇是什么？是OneCoder对《The Swift Programming Language》这篇官方文档的翻译和学习。</p>
</blockquote>
<hr />
<p>
	这期开始精简标题。。。原来的太长了&hellip;&hellip;又不想省略&ldquo;从零单排&rdquo;这几个字&hellip;&hellip;&hellip;&hellip;先这样吧。。</p>
<h2>
	注释(Comments)</h2>
<p>
	注释，是你的代码中不会执行的部分，通常用来作为批准或是对你自己的一个提醒。Swift编译器在编译代码时会忽略到注释。</p>
<p>
	Swift中的注释与C中的非常相近。单行注释以两个斜线（//）开头：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
// this is a comment
</pre>
<p>
	<br />
	你也可以写多行注释，以斜线、星号开头(/*)，星号、斜线结尾(*/):<br />
	&nbsp;</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
   /* this is also a comment,
    but written over multiple lines */
</pre>
<p>
	与C中的多行注释不同，Swift中的多行注释可以嵌套。你可以在第一个多行注释内部，嵌套第二个多行注释。第二个多行注释关闭后，再结束第一个：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
 /* this is the start of the first multiline comment
    /* this is the second, nested multiline comment */
    this is the end of the first multiline comment */
</pre>
<p>
	嵌套的多行注释可以使你在某大段的代码中已经存在多行注释的情况下，方便、快捷的注释掉该段代码。</p>
<p>
	分号(Semicolons)</p>
<p>
	与许多其他的语言不同，Swift不需要你在每行语句后添加分号(;)，当然，如果你愿意你也可以加上。不过，如果你想在一行中编写多个独立的语句，那么分号就是必须的了：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
   let cat = &quot;cat&quot;; println(cat)
    // prints &quot;cat&quot;
</pre>
<p>
	午休时间翻译，貌似是&ldquo;短&rdquo;了一点~~考虑下面的类型部分作为一个整体~未完待续&hellip;&hellip;下一篇：<a href="http://www.coderli.com/30old-fromzero-alone-swift-programming-language-translate-4">基础 整型和浮点型</a></p>

