---
layout: post
title: 国外Netty教程翻译—Part2—创建Channel连接
date: 2013-12-22 11:43
author: onecoder
comments: true
categories: [Java, Netty, Netty]
---
<div>
	本文是Netty教程的第二篇。</div>
<div>
	&nbsp;</div>
<div>
	Channel不是直接被创建的，而是通过ChannelFactory来创建。ChannelFactory有2种类别，一种用来实现客户端Channel，另一种用于服务器端的Channel。对于这2种分类，都有不同的实现类来处理相应的I/O通讯协议：</div>
<div>
	&nbsp;</div>
<ul>
	<li>
		TCP NIO Channels: NioClientSocketChannelFactory 和 NioServerSocketChannelFactory</li>
	<li>
		UDP NIO Channels: NioDatagramChannelFactory</li>
	<li>
		TCP OIO Channels: OioClientSocketChannelFactory和OioServerSocketChannelFactory</li>
	<li>
		UDP OIO Channels: OioDatagramChannelFactory</li>
</ul>
<div>
	UDP的ChannelFactory在客户端和服务器端的实现相同的，因为UDP是无连接协议。还有其他2种类型：</div>
<div>
	&nbsp;</div>
<div>
	HTTP 客户端: &nbsp;HttpTunnelingClientSocketChannelFactory: 这个ChannelFactory能够方便地生成channel用于通过指定的Netty Servlet来 连接Netty服 务器。</div>
<div>
	Local Channels: DefaultLocalClientChannelFactory 和 DefaultLocalServerChannelFactory 分别是客户端和服务器端In-VM类 型的Channel组 件，这种Channel貌似是网络Channel实际上是在同一个JVM中来处理调用。这样在某些情况下，本地请求可以通过统一的Channel抽象接口被分发、处理。</div>
<div>
	在本篇博客中主要涉及TCP NIO Channel， 但是要注意创建不同ChannelFactory的方式上有细微的差别。</div>
<div>
	&nbsp;</div>
<div>
	TCP NIO ChannelFactory的构造方法使用相同类型的参数，还有一些重载方法。基本上，这个Factory需 要2个 线程池/Executors：</div>
<div>
	&nbsp;</div>
<div>
	Boss线程：由这个线程池提供的线程是boss种类的，用于创建、连接、绑定socket，然后把这些socket传 给worker线程池。在服务器端每个监听的socket都 有一个boss线 程来处理。在客户端，只有一个boss线程来处理所有的socket。</div>
<div>
	Worker线程：Worker线 程执行所有的异步I/O。 他们不是通用的线程，开发人员需要注意不要把与其不同的任务赋给线程，这可能导致线程被阻塞、无法处理他们真正关心的任务，反过来会导致死锁和一些莫名其妙的性能问题。</div>
<div>
	在客户端只有一个boss线程，为什么NioClientSocketChannelFactory还需要一个Executors？</div>
<div>
	&nbsp;</div>
<div>
	boss线程能够被延迟加载，而且没有任务需要处理的时候可以被释放，但是在线程池中保留少量的线程比在需要的时候创建一个新的线程、然后在空闲的时候销毁它更有效率。</div>
<div>
	还有可能多个不同的ChannelFactory被创建，应该让这些ChannelFactory共用一个线程池，而不是每个工厂独享一个线程池。</div>
<div>
	因为NIO ChannelFactory是唯一可以异步的处理socket连接、服务器端socket的绑定，所以是唯一使用boss线 程池的ChannelFactory。 其他的种类有的使用虚拟连接（Local），有的是同步的连接（OIO） 或者无状态连接（UDP）。HttpTunelingClientSocketChannelFactory是客户端socket ChannelFactory的简单封装，是否使用boss线程是可选的，而且也没给它配置boss线程。</div>
<div>
	&nbsp;</div>
<div>
	关于ChannelFactory需要注意：在Netty中处理逻辑的过程中，ChannelFactory需要申请资源，包括线程池。如果使用ChannelFactory之 后，一定要调用它的releaseExternalResources()方 法来保证它申请的所有资源被释放。</div>
<div>
	&nbsp;</div>
<div>
	总之，发送东西到一个监听状态的服务器：</div>
<div>
	&nbsp;</div>
<div>
	创建一个Channel。</div>
<div>
	将Channel连接到远程监听的socket。</div>
<div>
	调用Channel的write(Object message)方法。</div>
<div>
	传对象到Channel很灵活？不是如此，如果按照下面的方式做会出现什么？</div>
<div>
	&nbsp;</div>
<div>
	<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
channel.write(newDate());
</pre>
</div>
<div>
	Netty会抛出这个异常：</div>
<div>
	&nbsp;</div>
<blockquote>
	<div>
		java.lang.IllegalArgumentException: unsupported message type: class java.util.Date</div>
</blockquote>
<div>
	那支持什么样的对象呢？ChannelBuffer。但是Channel有一个叫做Pipeline的构造器（准确的说是ChannelPipeline）。一个Pipeline是一组拦截器组成的，这些拦截器能够处理和转换传给他们的值。当一个拦截器处理完成后，处理后的值会被传给下一个拦截器。这些拦截器被称作ChannelHandler。Pipeline会严格保证ChannelHandler实例的顺序。通常，第一个ChannelHandler会 接收一个原始的ChannelBuffer，而最后一个ChannelHandler（被称作Sink）会任何转给它的东西输出。在Pipeline的某个流程中，你可以实现一个ChannelHandler来做些有用的操作。ChannelHandler只是一个标识性接口，没有任何方法，因此处理器的实现很灵活，但是任何一个处理器都需要相应或者转发ChannelEvent（这里有很多专业的术语）。</div>
<div>
	&nbsp;</div>
<div>
	这个ChannelEvent又是什么鬼东西呢？在这两段落中，把ChannelEvent当做一个含有ChannelBuffer的包裹。至于ChannelBuffer已经介绍过，它是Channel的基本数据单位。在下一篇中会有ChannelEvent的详细介绍。</div>
<div>
	&nbsp;</div>
<div>
	<span style="color: rgb(25, 25, 25); font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px; background-color: rgb(251, 251, 251);">原文链接：&nbsp;</span><a class="external" href="http://seeallhearall.blogspot.com/2012/05/netty-tutorial-part-1-introduction-to.html" rel="nofollow" style="margin: 0px; padding: 0px; font-size: 14px; vertical-align: baseline; background-color: rgb(251, 251, 251); outline: none; text-decoration: none; border-color: rgb(8, 155, 195); color: rgb(8, 155, 195); font-family: 微软雅黑, Verdana, sans-serif, 宋体; line-height: 22px;" target="_blank">seeallhearall.blogspot</a><span style="color: rgb(25, 25, 25); font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px; background-color: rgb(251, 251, 251);">&nbsp;翻译：&nbsp;</span><a href="http://www.importnew.com/" style="margin: 0px; padding: 0px; font-size: 14px; vertical-align: baseline; background-color: rgb(251, 251, 251); outline: none; text-decoration: none; border-color: rgb(8, 155, 195); color: rgb(8, 155, 195); font-family: 微软雅黑, Verdana, sans-serif, 宋体; line-height: 22px;">ImportNew.com&nbsp;</a><span style="color: rgb(25, 25, 25); font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px; background-color: rgb(251, 251, 251);">-&nbsp;</span><a href="http://www.importnew.com/author/liuhaibo" style="margin: 0px; padding: 0px; font-size: 14px; vertical-align: baseline; background-color: rgb(251, 251, 251); outline: none; text-decoration: none; border-color: rgb(8, 155, 195); color: rgb(8, 155, 195); font-family: 微软雅黑, Verdana, sans-serif, 宋体; line-height: 22px;">刘海波</a><br style="color: rgb(25, 25, 25); font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px; background-color: rgb(251, 251, 251);" />
	<span style="color: rgb(25, 25, 25); font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px; background-color: rgb(251, 251, 251);">译文链接：&nbsp;</span><a href="http://www.importnew.com/7674.html" style="margin: 0px; padding: 0px; font-size: 14px; vertical-align: baseline; background-color: rgb(251, 251, 251); outline: none; text-decoration: none; border-color: rgb(8, 155, 195); color: rgb(8, 155, 195); font-family: 微软雅黑, Verdana, sans-serif, 宋体; line-height: 22px;">http://www.importnew.com/7674.html</a></div>
<div>
	<br style="color: rgb(25, 25, 25); font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 16px; font-weight: bold; line-height: 22px; background-color: rgb(251, 251, 251);" />
	<img src="http://www.importnew.com/importnew-weixin.jpg" style="margin: 0px; padding: 0px; border: 0px; outline: 0px; font-size: 16px; vertical-align: baseline; background-color: rgb(251, 251, 251); color: rgb(25, 25, 25); font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-weight: bold; line-height: 22px;" /></div>

