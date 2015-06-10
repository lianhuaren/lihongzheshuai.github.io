---
layout: post
title: 从零单排iOS 番外篇 十、《The Swift Programming Language》翻译 基础 元组
date: 2014-06-12 13:53
author: onecoder
comments: true
categories: [swift, swift, 中文, 官方文档, 翻译]
---
<blockquote>
	<p>
		此番外篇系列是OneCoder对《The Swift Programming Language》这篇官方文档的翻译和学习。<br />
		绿豆粥&hellip;&hellip;&hellip;&hellip;快吐了&hellip;&hellip;&hellip;&hellip;好在今天翻译的内容感觉比较新鲜&hellip;&hellip;</p>
</blockquote>
<p>
	<em><strong>元组(Tuples)</strong></em></p>
<p>
	元组把多个值放入一个单独符合值中。元组中的值可以是任意类型，并且彼此之间不必的类型不必一致。</p>
<p>
	在这个例子中，（404， &quot;Not Found&quot;）就是一个描述HTTP状态码的元组。HTTP状态码是你请求一个网页时，网站服务器返回的一个特殊值。当你请求的网页不存在时，返回状态码404 Not Found。</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
   let http404Error = (404, &quot;Not Found&quot;)
    // http404Error is of type (Int, String), and equals (404, &quot;Not Found&quot;)
</pre>
<p>
	元组(404, &quot;Not Found&quot;)组合了Int和String类型，赋予HTTP状态码两个不同的值：一个数字和一个便于人们阅读理解的值。该元组可描述为一个关于（Int, String）类型的元组。</p>
<p>
	你可以构造任意类型排列的元组，且元组可以包含任意多种不同类型的值，随你喜好。你可以随意构造出如(Int,Int,Int)类型或(String,Bool)的数组，事实上任何其他排列组合都可以。</p>
<p>
	你可以把一个元组中的值拆分到不同的常量或变量中，然后你就可以想平常一样来使用：</p>
<br />
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
    let (statusCode, statusMessage) = http404Error
    println(&quot;The status code is \(statusCode)&quot;)
    // prints &quot;The status code is 404&quot;
    println(&quot;The status message is \(statusMessage)&quot;)
    // prints &quot;The status message is Not Found&quot;
</pre>
<br />
<p>
	如果你只需要元组中部分值，可以在拆分时使用下划线(_)来忽略元组中的值：</p>
<br />
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
    let (justTheStatusCode, _) = http404Error
    println(&quot;The status code is \(justTheStatusCode)&quot;)
    // prints &quot;The status code is 404&quot;
</pre>
<p>
	另外，你也可以利用索引数来访问元组中的独立元素，索引从0开始：</p>
<br />
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
    println(&quot;The status code is \(http404Error.0)&quot;)
    // prints &quot;The status code is 404&quot;
    println(&quot;The status message is \(http404Error.1)&quot;)
    // prints &quot;The status message is Not Found&quot;
</pre>
<p>
	你可以在定义元组中时，指定独立元素的名字:</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
let http200Status = (statusCode: 200, description: &quot;OK&quot;)
</pre>
<p>
	如果你定义了元组中元素的名字，那么就可以通过元素的名字来访问元组中的元素的值：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
    println(&quot;The status code is \(http200Status.statusCode)&quot;)
    // prints &quot;The status code is 200&quot;
    println(&quot;The status message is \(http200Status.description)&quot;)
    // prints &quot;The status message is OK&quot;
</pre>
<p>
	&nbsp;</p>
<p>
	元组作为函数的返回值特别有用。例如：用于请求网页的函数可以放回(Int,String)类型的元组用于描述此次请求是成功还是失败。返回一个包含两个不同类型值的元组，该函数返回的信息远比仅返回一个单独类型的值有用。更多信息，可参阅多返回值函数(Functions with Multiple Return Values. )部分</p>
<blockquote>
	<p>
		注意：</p>
	<p>
		对于临时组合相关值，元组非常有用。不过元组不适合用来构造复杂的数据结构。如果你的数据结构更适合持久化，超出了临时作用域的范畴，那么更适合将其定义成一个类(class)或结构体(structure)而不是元组。更多信息，可参阅类和结构体（Classes and Structures. ）部分。</p>
</blockquote>
<p>
	发现一个好碉堡的项目，swift中文教程，在GitHub上，30个人9天把《The Swift Programming Language》全翻译完了&hellip;&hellip;厉害啊。</p>
<p>
	对了，那个翻译好的文档，我随后也会提供链接和下载。</p>

