---
layout: post
title: 30岁码农从零单排iOS 番外篇 二、《The Swift Programming Language》翻译 基础 常量和变量
date: 2014-06-04 11:52
author: onecoder
comments: true
categories: [iOS, swift, swift, 翻译]
---
<blockquote>
	<p>
		什么是从零单排？DOTA大神们曾经流行的视频系列。不同的是，我是真正的从&ldquo;0&rdquo;单人自学，DOTA大神们是卖萌。 为什么从零单排？ 就想看看自己能不能坚持做好一件事情。<br />
		为什么是iOS？听起来高大上。移动互联时代，分杯翔也好。<br />
		为什么是30岁码农？因为30岁了，还是码农。<br />
		番外篇是什么？是OneCoder对《The Swift Programming Language》这篇官方文档的翻译和学习。</p>
</blockquote>
<hr />
<h2>
	常量和变量(Constans and Variables)</h2>
<p>
	常量和变量，把一个变量名(如：maximumNumberOfLoginAttempts 或者 welcomeMessage) 和一个特定类型的值相关联。（如：数字10或者字符串&quot;Hello&quot;）。常量的值在设置后就不可改变，变量的值可以在以后修改。</p>
<p>
	声明常量和变量（Declaring Constants and Variables）</p>
<p>
	常量和变量在使用前都必须声明。使用关键字let来声明一个常量，使用var关键字来声明一个变量。这里有一个如何使用常量和变量来记录用户登录次数的样例：</p>
<pre class="brush:scala;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
  let maximumNumberOfLoginAttempts = 10
    var currentLoginAttempt = 00
</pre>
<p>
	&nbsp; 代码说明如下：</p>
<p>
	声明一个名为：maximumNumberOfLoginAttempts的常量，赋值为10。然后声明一个新的名为currentLoginAttempt的变量，并赋初始值为0。</p>
<p>
	在这个例子中，允许尝试登录的最大次数声明为常量，因为该最大值不会改变。当前的尝试登录次数声明为变量，是因为该值必须随着每次用户尝试登录失败而增长。</p>
<p>
	你可以在一行中声明多个常量或是变量，用逗号来分割不同的常量或变量：</p>
<pre class="brush:scala;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
 var x = 0.0, y = 0.0, z = 0.0
</pre>
<blockquote>
	<p>
		注意：<br />
		如果你代码里的值不会被改变，那么就应该用let将其声明为常量。变量应该只用来存储需要改变的值。</p>
</blockquote>
<h2>
	类型声明(type annotations)</h2>
<p>
	你可以在声明常量或变量的时候使用类型声明，这样可以清楚表明声明的常量或变量可以存储的值的类型。编写类型声明的方式是，在常量或变量名后加上冒号，然后是空格，最后添加类型的名字。</p>
<p>
	下面的例子是对变量welcomeMessage使用类型声明，表明该变量可以存储String类型的值：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
var welcomeMessage: String
</pre>
<p>
	声明中的冒号代表&ldquo;类型是&rdquo;，所以上述代码的意思是：<br />
	&ldquo;声明一个String类型的，名为welcomeMessage的变量。&ldquo;</p>
<p>
	&rdquo;String类型&ldquo;的意味着可存储&rdquo;任何String类型的值&ldquo;。就可以把他理解成可以存储的东西的类型。</p>
<p>
	变量welcomeMessage现在可以无错的设置任何String类型的值:</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
welcomeMessage = &quot;Hello&quot;
</pre>
<blockquote>
	<p>
		注意：</p>
	<p>
		在练习中需要你指定类型声明的情况是比较少见的。如果你在定义的时候给定了某常量或变量的初始值，Swift会自动指定该常量或变量的类型，正如类型安全和类型推演部分介绍的那样。在上面的welcome<br />
		Message的例子中，没有给定初始值，因此welcomeMessage变量的类型通过类型声明指定而不是通过初始值推演的。</p>
</blockquote>
<h2>
	给常量和变量命名（Naming Constants and Variables）</h2>
<p>
	你几乎可以使用任何字符来给常量和变量命名，这其中还包括Unicode码:</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
let &pi; = 3.14159 

let 你好 = &quot;你好世界&quot;

let xxx ＝ &quot;dogCow&quot; （OneCoder注: 常量名为Unicode码）</pre>
<p>
	常量和变量名中不能包含数学符号，剪头，私有(或非法)的Unicode代码，以及中划线-和箱式绘图(box-drawing)的符号。<em>(OneCoder注：关于box-drawing介绍，参考：<a href="http://en.wikipedia.org/wiki/Box-drawing_character">http://en.wikipedia.org/wiki/Box-drawing_character</a>)</em>。变量名可以包含数字但是不能以数字开头。</p>
<p>
	如果你声明了一个指定类型的常量或是变量，你不能再次声明同名的变量，也不能用其存储不同类型的值。当然，你也不能将常量变为变量，反之亦不可。</p>
<blockquote>
	<p>
		注意：</p>
	<p>
		如果你需要使用Swift中的关键字给常量或是变量命名，你可以在用反引号(`)将关键字扩起来。不过，你应该尽量避免使用关键字来命名，除非你别无选择。</p>
</blockquote>
<p>
	你可以把已有的变量的值修改为另一个类型上兼容的值。在下面的例子中，变量friendlyWelcome的值从&rdquo;Hello&rdquo;变为&rdquo;Bonjour!&rdquo;:</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
var friendlyWelcome = &quot;Hello!&quot;
friendlyWelcome = &quot;Bonjour!&quot;
// friendlyWelcome is now &quot;Bonjour!&quot;
</pre>
<p>
	与变量不同，常量的值在指定后就无法改变。尝试改变常量的值会在代码的编译期报告一个错误:</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
let languageName = &quot;Swift&quot;
languageName = &quot;Swift++&quot;
// this is a compile-time error - languageName cannot be changed
</pre>
<p>
	打印常量和变量(Printing Constants and Variables)</p>
<p>
	你可以通过println函数来打印一个常量或变量的当前值：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
println(friendlyWelcome)
// prints &quot;Bonjour!&rdquo;
</pre>
<p>
	println是用来打印值到相应的输出端的全局函数，打印值后会换行。例如，如果你用Xcode工作，println函数会将输出打印到Xcode的控制台面板中。(另一个print函数，功能相同，只是打印后不会换行。)</p>
<p>
	<br />
	println函数会打印任何传递给他的String类型的值:</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
println(&quot;This is a string&quot;)
// prints &quot;This is a string&rdquo;
</pre>
<p>
	与Cocoa中的NSLog函数类型，println函数可以打印更加复杂的日志信息。这些信息可以包含当前的常量或变量的值。</p>
<p>
	<br />
	在长字符串中，Swift使用字符串插入符(string interpolation)包含常量或变量的名字来作为占位符，并且Swift会用常量或变量当前的值来替换它。使用方式为，将变量名字用括号括起来，并且在括号前添加反斜线：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
println(&quot;The current value of friendlyWelcome is \(friendlyWelcome)&quot;)
// prints &quot;The current value of friendlyWelcome is Bonjour!&rdquo;
</pre>
<blockquote>
	<p>
		注意：</p>
	<p>
		我们将在字符串插入符部分介绍所谓你可以使用的选项。</p>
</blockquote>
<p>
	卧床翻译，未完待续&hellip;&hellip;下一篇：<a href="http://www.coderli.com/30old-fromzero-alone-swift-programming-language-translate-3">注释和分号</a>。OneCoder的OC之路仍会继续，翻译只是调剂&hellip;&hellip;</p>

