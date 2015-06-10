---
layout: post
title: 国外Netty教程翻译系列—Part1—介绍 
date: 2013-12-21 09:26
author: onecoder
comments: true
categories: [Java, Netty, Netty]
---
<div>
	摘自Netty官网：</div>
<div>
	&nbsp;</div>
<blockquote>
	<div>
		&ldquo;Netty是一个NIO C/S框架，能够快速、简单的开发协议服务器和客户端等网络应用。它能够很大程度上简单化、流水线化开发网络应用，例如TCP/UDP socket服务器。&rdquo;</div>
</blockquote>
<div>
	&nbsp;</div>
<div>
	Netty是一个主要用于编写高并发网络系统、网络应用和服务的Java库。Netty和 标准的Java APIs一个主要的差别在于它的异步API。这个名词对于不同的人来说意味着不同的东西，可能和非阻塞、事件驱动有相同的意义。先不管这些，如果你之前从未使用过异步API，习惯于编写顺序执行的程序，那么转换思路编写Netty程序有点麻烦。在这里简要的介绍我如何解决这个问题。</div>
<div>
	&nbsp;</div>
<div>
	编写一个Netty示例并启动。和其他Java API一样，发起request很容易。在处理response需要转换思维，因为没有response。几乎每个方法调用都是异步的，这意味着没有返回值，而且调用常常是立刻返回的。结果（如果有的话）是 由 其他线程传回的。这是普通API和异步API的基本区别。假如一个客户端API提 供一个方法来从服务器获取widget的 数量。</div>
<div>
	&nbsp;</div>
<div>
	标准API</div>
<div>
	&nbsp;</div>
<div>
	<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
public int getWidgetCount();
</pre>
</div>
<div>
	&nbsp;</div>
<div>
	当一个线程调用getWidgetCount(),一段时间过后一个int值会被会返回。</div>
<div>
	&nbsp;</div>
<div>
	异步API</div>
<div>
	&nbsp;</div>
<div>
	<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
publicWidgetCountListener myListener = newWidgetCountListener() {
     publicvoidonWidgetCount(intwidgetCount) {
          ...... doyour thing with the widget count
     }
};
</pre>
</div>
<div>
	在我举例的getWidgeCount API异步版本中，方法调用没有返回数据，而且即刻执行完毕。然而，它接受了一个response处理器参数，当获取到widget数 据会回调这个监听器，然后监听器可以利用这个结果执行任意有用的操作。</div>
<div>
	&nbsp;</div>
<div>
	这个复杂的附加层看起来是不必要的，但这却是利用Netty等API实现的高性能的应用、服务的一个关键特征。客户端不需要为线程被阻塞在等待服务器响应而浪费资源。当数据可用时会通知他们。对于一个线程发起一个调用的情况，这种方式可能有些过度设计，但是设想有上百个线程百万次的执行这个方法。而且，NIO最重要的好处是Selectors能够用来将我们感兴趣的事件通知委托给底层的操作系统，甚至是硬件来完成。当16bytes已经成功从网络写入到一个服务器或者14bytes经过网络从一个服务器读到本地等操作会被回调通知，很明显是一个底层而且细节的实现方式。但是通过一系列的抽象，开发人员使用Java NIO和Netty API能 够从更宏观、抽象的级别上来处理这些问题。</div>
<div>
	&nbsp;</div>
<div>
	在本篇介绍中，我会从一些基本的概念讲起，然后列举部分核心的构建代码块，最后介绍一些代码示例。</div>
<div>
	&nbsp;</div>
<div>
	这些是如何工作的呢？</div>
<div>
	&nbsp;</div>
<div>
	Netty的基本数据结构是ChannelBuffer。Netty的JavaDocs定义：</div>
<div>
	&nbsp;</div>
<blockquote>
	<div>
		一个可以被随机或顺序访问任意个数字节的序列。这个接口为原生byte数组(byte[])和Java NIO buffers提供了一个抽象的概念。</div>
</blockquote>
<div>
	Netty中数据如何被分发也是由ChannelBuffer完成的。如果你的程序只是处理byte数组和byte缓冲区，那么你会很幸运因为使用ChannelBuffer。 然而，如果你需要处理更高级的数据，例如java对象，把这个数据发送到一个远程服务器，然后再接受另外一个返回的对象，那么这些byte计划需要被转义。或者，如果你需要发起一个请求到一个HTTP服务器，你的请求或许是从发起一个字符串类型URL来开始它的生命周期，但是它需要被包装成一个HTTP服 务器能够理解的request，然后分解为原始的byte，最后通过网络发送。HTTP服务器需要接收这些byte，并且把它们解析一个符合格式的HTTP request。一旦请求成功，这个流程会被反过来执行：服务器的响应（或许是一个JPEG图片或者JavaScript文件）必须被包装成一个HTTP response，转换为bytes，然后返回给发起请求的客户端。</div>
<div>
	&nbsp;</div>
<div>
	在Netty中，byte在网络流通的基本抽象是Channel。 再看一下Netty的API说明：</div>
<div>
	&nbsp;</div>
<blockquote>
	<div>
		网络socket或者网络组件的连接器（Nexus）可以实现I/O操作，例如：读、写、连接和绑定。</div>
</blockquote>
<div>
	用Nexus来描述Channel是 很恰当的（来自dictionary.com对nexus的解释）：</div>
<div>
	&nbsp;</div>
<ol>
	<li>
		a means of connection; tie; link</li>
	<li>
		a connected series or group</li>
	<li>
		the core or center, as of a matter or situation</li>
</ol>
<div>
	&nbsp;</div>
<div>
	Channel被提供给用户来与Netty交互的API。更符合实际情况的说法，Channel是socket的抽象描述。但是Channel不一定是一个socket，也可以是一个文件、或者其他抽象的东西。因此，Nexus很适合描述Channel。总而言之，Channel提供接口来连接目标，并写入数据。你或许会疑问为什么没有读操作？确实没有。要知道，这和上面提到的异步getWidgetCount方法一样，没有返回值。</div>
<div>
	&nbsp;</div>
<div>
	所有基于Channel的方法可以分为2种：</div>
<div>
	&nbsp;</div>
<ol>
	<li>
		同步的属性获取方法：用于提供Channel本身的信息。</li>
	<li>
		I/O操 作，例如：绑定、断开连接、写入。</li>
</ol>
<div>
	类型2中的方法是异步的，他们都返回一个ChannelFuture的延迟对象。ChannelFuture是保存未知结果的容器，但是结果可用时肯定会被返回的。ChannelFuture有点像WidgetCountListener，不同的地方在于你需要把ChannelFuture注册到你的监听器中，而不是把监听器注册到你的代码流程中。（更简单的API，对吧？）你可以实现ChannelFutureListener接口来完成一个监听器，这个接口有一个方法会在操作完成的时候被调用：public void operationComplete(ChannelFuture future)。在操作完成后这个方法被调用，但并不意味这操作成功，所以这个延迟对象可以用来查询操作的最终结果-成功或失败。</div>
<div>
	&nbsp;</div>
<div>
	Netty主要用于NIO，但Netty的Channel实现类支持老版本的、同步的IO（OIO）。OIO有一些优点，而且Netty对它的实现方式和NIO一样，这些实现模块能够被替换和重用。</div>
<div>
	&nbsp;</div>
<div>
	<span style="color: rgb(25, 25, 25); font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px; background-color: rgb(251, 251, 251);">原文链接：&nbsp;</span><a class="external" href="http://seeallhearall.blogspot.com/2012/05/netty-tutorial-part-1-introduction-to.html" rel="nofollow" style="margin: 0px; padding: 0px; font-size: 14px; vertical-align: baseline; background-color: rgb(251, 251, 251); outline: none; text-decoration: none; border-color: rgb(8, 155, 195); color: rgb(8, 155, 195); font-family: 微软雅黑, Verdana, sans-serif, 宋体; line-height: 22px;" target="_blank">seeallhearall.blogspot</a><span style="color: rgb(25, 25, 25); font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px; background-color: rgb(251, 251, 251);">&nbsp;翻译：&nbsp;</span><a href="http://www.importnew.com/" style="margin: 0px; padding: 0px; font-size: 14px; vertical-align: baseline; background-color: rgb(251, 251, 251); outline: none; text-decoration: none; border-color: rgb(8, 155, 195); color: rgb(8, 155, 195); font-family: 微软雅黑, Verdana, sans-serif, 宋体; line-height: 22px;">ImportNew.com&nbsp;</a><span style="color: rgb(25, 25, 25); font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px; background-color: rgb(251, 251, 251);">-&nbsp;</span><a href="http://www.importnew.com/author/liuhaibo" style="margin: 0px; padding: 0px; font-size: 14px; vertical-align: baseline; background-color: rgb(251, 251, 251); outline: none; text-decoration: none; border-color: rgb(8, 155, 195); color: rgb(8, 155, 195); font-family: 微软雅黑, Verdana, sans-serif, 宋体; line-height: 22px;">刘海波</a><br style="color: rgb(25, 25, 25); font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px; background-color: rgb(251, 251, 251);" />
	<span style="color: rgb(25, 25, 25); font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px; background-color: rgb(251, 251, 251);">译文链接：&nbsp;</span><a href="http://www.importnew.com/7669.html" style="margin: 0px; padding: 0px; font-size: 14px; vertical-align: baseline; background-color: rgb(251, 251, 251); outline: none; text-decoration: none; border-color: rgb(8, 155, 195); color: rgb(8, 155, 195); font-family: 微软雅黑, Verdana, sans-serif, 宋体; line-height: 22px;">http://www.importnew.com/7669.html</a></div>
<div>
	<img src="http://www.importnew.com/importnew-weixin.jpg" style="margin: 0px; padding: 0px; border: 0px; outline: 0px; font-size: 16px; vertical-align: baseline; background-color: rgb(251, 251, 251); color: rgb(25, 25, 25); font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-weight: bold; line-height: 22px;" /></div>

