---
layout: post
title: 代码之丑 构造函数
date: 2012-07-06 08:00
author: onecoder
comments: true
categories: [Java基础, 代码之丑, 构造函数]
---
<p>
	<span>代码评审，我对一个TreeSet产生了兴趣。</span><br />
	&nbsp;</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
TreeSet configuration = new TreeSet();
...
Handler handler = new Handler(configuration);
</pre>
<p>
	&nbsp;</p>
&ldquo;为什么要用TreeSet呢？&rdquo;，我问道。<br />
&ldquo;因为这是构造函数的参数决定的。&rdquo;，有人回答。<br />
&ldquo;可以打开源码看一下吗？&rdquo;，对于这种处理，通常人们都会选择HashSet，好奇心驱使我要进一步专研一下这段代码。<br />
<br />
我看到了这个构造函数的声明：<br />
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
public Handler(TreeSet configuration) {
  ...
}
</pre>
在我开始研究这个构造函数使用TreeSet的缘由之前，我看到了另外一个构造函数，或许它更能满足我的心理需求：<br />
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
public Handler(HashSet configuration) {
  ...
}</pre>
&ldquo;为什么会有一个用到HashSet构造函数？它和用到TreeSet的有什么不同&rdquo;，我继续追问。<br />
&ldquo;它们是分别处理两种情况的，在不同的配置下起作用。&rdquo;<br />
<br />
我终于知道为什么会有TreeSet，因为HashSet已经被人用了，为了支持另外一种情形，TreeSet被人从墙角了挖了出来。可是如果不深究代码，谁又能知道这其中的奥妙呢？显然，我们需要一个更具表达力的写法。<br />
<br />
之所以陷入这样的坑，根源在于构造函数，因为构造函数只能有一个名字。其实，这里只是要解决构造的问题，而面对这个问题，解决方式几乎再直白不过了：工厂方法。<br />
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
class HandlerFactory {
  public static Handler createTrivialHandler(Set configuration) {
    ...
  }

  public static Handler createFancyHandler(Set configuration) {
    ...
  }
}
</pre>
这里，用两个名字上有更明确意义的函数替代之前的那两个需要强大理解力的构造函数。当然，这里的参数用了Set，连具体的类型都省了，真正的面向接口编程。<br />
<br />
事实上，如果一个类有多于一个的构造函数，都是值得考虑的。我曾写过一篇《构造函数沉思录》专门讨论这个问题。<br />
<br />
<span style="font-family: Lucida, 'Lucida Grande', Tahoma, sans-serif; font-size: 12px; line-height: 19px; background-color: rgb(255, 255, 255); ">作者&nbsp;</span><strong style="color: rgb(28, 73, 123); padding: 0px 2px; font-family: Lucida, 'Lucida Grande', Tahoma, sans-serif; font-size: 12px; line-height: 19px; background-color: rgb(255, 255, 255); "><a class="editorlink f_taxonomyEditor" href="http://www.infoq.com/cn/author/%E9%83%91%E6%99%94" style="text-decoration: none; color: rgb(28, 73, 123) !important; padding: 0px 2px; ">郑晔<br />
<span style="font-size: 13px; ">原文：</span></a><a href="http://dreamhead.blogbus.com/logs/216314257.html" style="color: rgb(11, 89, 178); font-size: 13px; ">代码之丑（十四）</a></strong>
