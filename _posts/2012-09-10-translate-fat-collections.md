---
layout: post
title: 给你臃肿的集合(Collections)瘦身
date: 2012-09-10 13:39
author: onecoder
comments: true
categories: [collections, Java, 原创翻译, 翻译]
---
<p>
	<a href="http://www.coderli.com">OneCoder</a>在研究的过程中，看到一系列关于Java内存占用的好文，于是就迫不及待的想翻译出来与大家分享。今天翻译的是关于Java中集合内存消耗相关的文章。很有学习价值。</p>
<div>
	以下是翻译：</div>
<div>
	Java的Collection接口是Java中最常用的接口之一。它给我们提供了日常所用数据结构方便稳定的实现和使用方式。但是，它足以合适的解决所有问题吗？</div>
<div>
	&nbsp;</div>
<div>
	在这篇文章中，我将研究Java的Collection接口一个会消耗巨大内存的使用场景。</div>
<div>
	&nbsp;</div>
<div>
	背景</div>
<div>
	&nbsp;</div>
<div>
	每个Java程序都会使用各种各样的数据结构，如简单的数组或者斐波那契堆或者一些独特的只有Google才会知道的结构。在多数时候，开发者并不会开发自己的实现，而是使用Java核心接口或者其他第三方包提供的接口，如Apache Collections或是Google Guava。在我十余年的Java开发生涯中，没有一天不再使用这些Java Collection接口。这些List, Sets和Maps对于我来说非常的熟悉，我可以不假思索的写出下面的代码：</div>
<div>
	Map&lt;Integer, String&gt; = new HashMap&lt;Integer, String&gt;();</div>
<div>
	&nbsp;</div>
<div>
	然后，最近事情有所改变...</div>
<div>
	&nbsp;</div>
<div>
	在我们的Plumbr Java代理中需要使用一个数据结构去存储大量的整数。需求大致如下：</div>
<div>
	<ul>
		<li>
			我们需要一个数据结构去保存整数</li>
		<li>
			没有重复</li>
		<li>
			不在乎顺序</li>
		<li>
			需要可以向其中添加元素</li>
		<li>
			需要查询元素是否在这个结构中</li>
		<li>
			不同元素的数量级最多是几百</li>
		<li>
			内存的消耗的优先级高于速度</li>
		<li>
			无论如何性能要高，所以MemoryMapped文件，数据库等不在考虑之列</li>
	</ul>
	<div>
		很显然，根据这些需求和我的经验java.util.HashSet&lt;Integer&gt;是不二的选择。所以，我不假思索的选择了HashSet。噩梦就此开始。</div>
</div>
<div>
	&nbsp;</div>
<div>
	<span style="font-size:22px;"><strong>实验</strong></span></div>
<div>
	&nbsp;</div>
<div>
	为了说明我的观点，我们需要一些方式去衡量不同数据结构的内存消耗。为了写篇博文，我计划的步骤是这样的：</div>
<div>
	<ol>
		<li>
			写一个有Main函数的Java 类，定义我们需要的数据结构作为本地变量。</li>
		<li>
			在方法尾部加入死循环，以使该线程不会立即结束。</li>
		<li>
			使用Eclipse Memory Analyzer（内存分析）工具将内存dump出来，分析本地变量所占用本地保留堆的大小。</li>
	</ol>
	<div>
		我的分析基于以下事实，一个Java的整数占用4字节。因此，对于N个整数，我们需要4*N个字节存储。然后，我们可以计算出给定的数据结构的内存消耗比率：</div>
	<blockquote>
		<div>
			Overhead = Structure&#39;s retained heap/(4*COUNT)(<a href="http://www.coderli.com">OneCoder</a>注：消耗比率=数据结构占用总内存数/4*整数个数)</div>
	</blockquote>
	<div>
		值得一提的是，Java是区分基本数据类型和Object对象的，并且Collection API仅仅可以操作Objects类型。这意味着，总个内存消耗比率包括数据结构本身的内存占用和Integer整型对象的消耗比率，而不是基本数据库类型。
		<div>
			&nbsp;</div>
		<div>
			<strong><span style="font-size:22px;">结果：</span></strong></div>
		<div>
			&nbsp;</div>
		<div>
			经过这些准备，我们来估算一下java.util.HashSets有多大。我使用以下代码：</div>
		<div>
			<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
Set&lt;Integer&gt; set = new HashSet&lt;Integer&gt;();
int COUNT = 10;
for (int i = 0; i &lt; COUNT; i++) {
   set.add(i);
}
</pre>
		</div>
	</div>
</div>
<p>
	我们来看下结果：</p>
<table style="margin: 20px 0px 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: baseline; color: rgb(40, 47, 52); font-family: Georgia, Arial, sans-serif; line-height: 20px; text-align: justify; background-color: rgb(255, 255, 255); " width="100%">
	<tbody style="margin-top: 0px; margin-right: 0px; margin-bottom: 0px; margin-left: 0px; padding-top: 0px; padding-right: 0px; padding-bottom: 0px; padding-left: 0px; border-top-width: 0px; border-right-width: 0px; border-bottom-width: 0px; border-left-width: 0px; border-style: initial; border-color: initial; border-image: initial; font: inherit; vertical-align: baseline; ">
		<tr style="margin-top: 0px; margin-right: 0px; margin-bottom: 0px; margin-left: 0px; padding-top: 0px; padding-right: 0px; padding-bottom: 0px; padding-left: 0px; border-top-width: 0px; border-right-width: 0px; border-bottom-width: 0px; border-left-width: 0px; border-style: initial; border-color: initial; border-image: initial; font: inherit; vertical-align: baseline; ">
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				<b style="margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: baseline; ">COUNT</b></td>
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				<b style="margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: baseline; ">Retained heap</b></td>
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				<b style="margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: baseline; ">Overhead</b></td>
		</tr>
		<tr style="margin-top: 0px; margin-right: 0px; margin-bottom: 0px; margin-left: 0px; padding-top: 0px; padding-right: 0px; padding-bottom: 0px; padding-left: 0px; border-top-width: 0px; border-right-width: 0px; border-bottom-width: 0px; border-left-width: 0px; border-style: initial; border-color: initial; border-image: initial; font: inherit; vertical-align: baseline; ">
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				10</td>
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				720</td>
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				18</td>
		</tr>
		<tr style="margin-top: 0px; margin-right: 0px; margin-bottom: 0px; margin-left: 0px; padding-top: 0px; padding-right: 0px; padding-bottom: 0px; padding-left: 0px; border-top-width: 0px; border-right-width: 0px; border-bottom-width: 0px; border-left-width: 0px; border-style: initial; border-color: initial; border-image: initial; font: inherit; vertical-align: baseline; ">
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				100</td>
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				6 960</td>
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				17.4</td>
		</tr>
		<tr style="margin-top: 0px; margin-right: 0px; margin-bottom: 0px; margin-left: 0px; padding-top: 0px; padding-right: 0px; padding-bottom: 0px; padding-left: 0px; border-top-width: 0px; border-right-width: 0px; border-bottom-width: 0px; border-left-width: 0px; border-style: initial; border-color: initial; border-image: initial; font: inherit; vertical-align: baseline; ">
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				1000</td>
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				85 424</td>
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				21.36</td>
		</tr>
		<tr style="margin-top: 0px; margin-right: 0px; margin-bottom: 0px; margin-left: 0px; padding-top: 0px; padding-right: 0px; padding-bottom: 0px; padding-left: 0px; border-top-width: 0px; border-right-width: 0px; border-bottom-width: 0px; border-left-width: 0px; border-style: initial; border-color: initial; border-image: initial; font: inherit; vertical-align: baseline; ">
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				1000000</td>
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				88 774 256</td>
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				22.19</td>
		</tr>
	</tbody>
</table>
<p>
	哇！用java.util.HashSet保存整型需要大于数据本身大概20倍内存的存储空间。对我来说，这是一个巨大的比率。考虑到我们的需求是要求 有限的内存使用情况，这是无法接受的。我们必须考虑其他的方案。我们开始重审我们的需求：什么才是我们需要的？最普通的java数组应该可以满足我们的需求。修改我的代码如下：</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
int COUNT = 10; 
Integer[] array = new Integer[COUNT];
for (int i = 0; i &lt; COUNT; i++) {
    array[i] = i;
} </pre>
<p>
	结果如下：</p>
<table style="margin: 20px 0px 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: baseline; color: rgb(40, 47, 52); font-family: Georgia, Arial, sans-serif; line-height: 20px; text-align: justify; background-color: rgb(255, 255, 255); " width="100%">
	<tbody style="margin-top: 0px; margin-right: 0px; margin-bottom: 0px; margin-left: 0px; padding-top: 0px; padding-right: 0px; padding-bottom: 0px; padding-left: 0px; border-top-width: 0px; border-right-width: 0px; border-bottom-width: 0px; border-left-width: 0px; border-style: initial; border-color: initial; border-image: initial; font: inherit; vertical-align: baseline; ">
		<tr style="margin-top: 0px; margin-right: 0px; margin-bottom: 0px; margin-left: 0px; padding-top: 0px; padding-right: 0px; padding-bottom: 0px; padding-left: 0px; border-top-width: 0px; border-right-width: 0px; border-bottom-width: 0px; border-left-width: 0px; border-style: initial; border-color: initial; border-image: initial; font: inherit; vertical-align: baseline; ">
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				<b style="margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: baseline; ">COUNT</b></td>
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				<b style="margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: baseline; ">Retained heap</b></td>
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				<b style="margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: baseline; ">Overhead</b></td>
		</tr>
		<tr style="margin-top: 0px; margin-right: 0px; margin-bottom: 0px; margin-left: 0px; padding-top: 0px; padding-right: 0px; padding-bottom: 0px; padding-left: 0px; border-top-width: 0px; border-right-width: 0px; border-bottom-width: 0px; border-left-width: 0px; border-style: initial; border-color: initial; border-image: initial; font: inherit; vertical-align: baseline; ">
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				10</td>
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				344</td>
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				8.6</td>
		</tr>
		<tr style="margin-top: 0px; margin-right: 0px; margin-bottom: 0px; margin-left: 0px; padding-top: 0px; padding-right: 0px; padding-bottom: 0px; padding-left: 0px; border-top-width: 0px; border-right-width: 0px; border-bottom-width: 0px; border-left-width: 0px; border-style: initial; border-color: initial; border-image: initial; font: inherit; vertical-align: baseline; ">
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				100</td>
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				3 224</td>
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				8.06</td>
		</tr>
		<tr style="margin-top: 0px; margin-right: 0px; margin-bottom: 0px; margin-left: 0px; padding-top: 0px; padding-right: 0px; padding-bottom: 0px; padding-left: 0px; border-top-width: 0px; border-right-width: 0px; border-bottom-width: 0px; border-left-width: 0px; border-style: initial; border-color: initial; border-image: initial; font: inherit; vertical-align: baseline; ">
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				1000</td>
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				32 024</td>
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				8.006</td>
		</tr>
		<tr style="margin-top: 0px; margin-right: 0px; margin-bottom: 0px; margin-left: 0px; padding-top: 0px; padding-right: 0px; padding-bottom: 0px; padding-left: 0px; border-top-width: 0px; border-right-width: 0px; border-bottom-width: 0px; border-left-width: 0px; border-style: initial; border-color: initial; border-image: initial; font: inherit; vertical-align: baseline; ">
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				1000000</td>
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				32 000 024</td>
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				8.000006</td>
		</tr>
	</tbody>
</table>
<p>
	已经不错了。跟HashSet比，比率降低了2-3倍。不过，我仍感觉太大了。所以，再将Integer[]改为int[]:</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
int COUNT = 10; 
int[] array = new int[COUNT];
for (int i = 0; i &lt; COUNT; i++) {
    array[i] = i;
}
</pre>
<p>
	得到：</p>
<table style="margin: 20px 0px 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: baseline; color: rgb(40, 47, 52); font-family: Georgia, Arial, sans-serif; line-height: 20px; text-align: justify; background-color: rgb(255, 255, 255); " width="100%">
	<tbody style="margin-top: 0px; margin-right: 0px; margin-bottom: 0px; margin-left: 0px; padding-top: 0px; padding-right: 0px; padding-bottom: 0px; padding-left: 0px; border-top-width: 0px; border-right-width: 0px; border-bottom-width: 0px; border-left-width: 0px; border-style: initial; border-color: initial; border-image: initial; font: inherit; vertical-align: baseline; ">
		<tr style="margin-top: 0px; margin-right: 0px; margin-bottom: 0px; margin-left: 0px; padding-top: 0px; padding-right: 0px; padding-bottom: 0px; padding-left: 0px; border-top-width: 0px; border-right-width: 0px; border-bottom-width: 0px; border-left-width: 0px; border-style: initial; border-color: initial; border-image: initial; font: inherit; vertical-align: baseline; ">
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				<b style="margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: baseline; ">COUNT</b></td>
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				<b style="margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: baseline; ">Retained heap</b></td>
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				<b style="margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: baseline; ">Overhead</b></td>
		</tr>
		<tr style="margin-top: 0px; margin-right: 0px; margin-bottom: 0px; margin-left: 0px; padding-top: 0px; padding-right: 0px; padding-bottom: 0px; padding-left: 0px; border-top-width: 0px; border-right-width: 0px; border-bottom-width: 0px; border-left-width: 0px; border-style: initial; border-color: initial; border-image: initial; font: inherit; vertical-align: baseline; ">
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				10</td>
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				64</td>
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				1.6</td>
		</tr>
		<tr style="margin-top: 0px; margin-right: 0px; margin-bottom: 0px; margin-left: 0px; padding-top: 0px; padding-right: 0px; padding-bottom: 0px; padding-left: 0px; border-top-width: 0px; border-right-width: 0px; border-bottom-width: 0px; border-left-width: 0px; border-style: initial; border-color: initial; border-image: initial; font: inherit; vertical-align: baseline; ">
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				100</td>
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				424</td>
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				1.06</td>
		</tr>
		<tr style="margin-top: 0px; margin-right: 0px; margin-bottom: 0px; margin-left: 0px; padding-top: 0px; padding-right: 0px; padding-bottom: 0px; padding-left: 0px; border-top-width: 0px; border-right-width: 0px; border-bottom-width: 0px; border-left-width: 0px; border-style: initial; border-color: initial; border-image: initial; font: inherit; vertical-align: baseline; ">
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				1000</td>
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				4 024</td>
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				1.006</td>
		</tr>
		<tr style="margin-top: 0px; margin-right: 0px; margin-bottom: 0px; margin-left: 0px; padding-top: 0px; padding-right: 0px; padding-bottom: 0px; padding-left: 0px; border-top-width: 0px; border-right-width: 0px; border-bottom-width: 0px; border-left-width: 0px; border-style: initial; border-color: initial; border-image: initial; font: inherit; vertical-align: baseline; ">
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				1000000</td>
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				4 000 024</td>
			<td style="word-break: break-all; margin: 0px; padding: 0px; border-width: 0px; font: inherit; vertical-align: top; text-align: left; ">
				1.000006</td>
		</tr>
	</tbody>
</table>
<div>
	现在情况就好多了。我们得到了稳定不变的比率，并且实现我们的需要的操作，如元素添加和去重，也是非常的容易。</div>
<div>
	&nbsp;</div>
<div>
	<span style="font-size:22px;"><strong>结论：</strong></span></div>
<div>
	&nbsp;</div>
<div>
	我们得到的第一个结论很显然：基本数据类型的数组是最紧凑的数据结构。这并不意外：）最令我吃惊的其实是Java Collection API的比率之大。我希望在下一次我选择数据结构的时候在我脑海中留有印象。</div>
<div>
	&nbsp;</div>
<div>
	当然，Java Collection API不会因为这个结论而不推荐使用。相反，我也已经会一次又一次的使用他，因为他提供非常简单方便的API。但是，在那些少数的关心每一个字节使用的场景，你最好验证清楚这个比率然后设计好你的软件的每个细节。</div>
<div>
	&nbsp;</div>
<div>
	另一个比率会显得很重要的场景是，当你想要用例如HashSet保存大量的数据的时候。我的意思是，在你需要保存时数以百万计的元素的时候，这个比率将会迅速增长达到一个很高的比率。此时，数据结构本身就会占用你大量的堆空间。</div>
<div>
	&nbsp;</div>
<div>
	原文地址：<a href="http://plumbr.eu/blog/fat-collections">http://plumbr.eu/blog/fat-collections</a></div>

