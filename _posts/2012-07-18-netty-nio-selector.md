---
layout: post
title: Java NIO框架Netty教程(六)-Java NIO Selector模式
date: 2012-07-18 21:19
author: onecoder
comments: true
categories: [Netty, Netty, nio, selector]
---
<p>
	<span style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">看到标题，您可能觉得，这跟Netty有什么关系呢？确实，如果你完全是使用Netty的，那么可能你可以完全不需要了解Selector。但是，不得不提的是，Netty底层关于NIO的实现也是基于Java的Selector的，是对Selector的封装。所以，我个人认为理解好Selector对于使用和理解Netty都是很多有帮助的。当然，如果您确实不关心这些，只想会用Netty就可以了。那么下文，您可以略过：）</span></p>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	笔者对于Selector也是新上手学习的。之前很多新人跟我交流，都会提到一个新框架或者一个新开源工具的使用和上手的问题。他们会觉得上手困难，耗费事件。不过笔者，从来没有此种感觉。这里正好，借用Selector的学习过程，跟大家交流一下，我上手的过程。</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	&nbsp;</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	想要使用一个工具，自然是先了解其定位，解决问题的原理或者工作流程。所以，笔者先从网上了解了一下Selector大概的工作流程。</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	&nbsp;</div>
<blockquote>
	<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
		NIO 有一个主要的类Selector,这个类似一个观察者，只要我们把需要探知的socketchannel告诉Selector,我们接着做别的事情，当有事件发生时，他会通知我们，传回一组SelectionKey,我们读取这些Key,就会获得我们刚刚注册过的socketchannel,然后，我们从这个Channel中读取数据，放心，包准能够读到，接着我们可以处理这些数据。</div>
</blockquote>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	&nbsp;</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	<span style="text-align: -webkit-auto; ">这是笔者摘录的一小段总结，就这一小段基本已经可以说明问题了。接下来，我们要考虑的就是，要实现这个过程，我们需要做什么？顺着描述，我们可以想象，需要选择器，需要消息传送的通道，需要注册一个事件，用于识别。通道自然需要绑定到一个地址。有了这样大概的想法，我们就可以去API里找相关的接口。</span>
	<div style="text-align: -webkit-auto; ">
		&nbsp;</div>
	<div style="text-align: -webkit-auto; ">
		Selector服务端样例代码：</div>
	<div style="text-align: -webkit-auto; ">
		<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
/**
 * Java NIO Select模式服务端样例代码
 * 
 * @author lihzh
 * @alia OneCoder
 * @Blog http://www.coderli.com
 * @date 2012-7-16 下午9:22:53
 */
public class NioSelectorServer {

	/**
	 * @author lihzh
	 * @throws IOException
	 * @alia OneCoder
	 * @date 2012-7-16 下午9:22:53
	 */
	public static void main(String[] args) throws IOException {
		// 创建一个selector选择器
		Selector selector = Selector.open();
		// 打开一个通道
		ServerSocketChannel socketChannel = ServerSocketChannel.open();
		// 绑定到9000端口
		socketChannel.socket().bind(new InetSocketAddress(8000));
		// 使设定non-blocking的方式。
		socketChannel.configureBlocking(false);
		// 向Selector注册Channel及我们有兴趣的事件
		socketChannel.register(selector, SelectionKey.OP_ACCEPT);
		for (;;) {
			// 选择事件
			selector.select();
			// 当有客户端准备连接到服务端时，便会出发请求
			for (Iterator&lt;SelectionKey&gt; keyIter = selector.selectedKeys()
					.iterator(); keyIter.hasNext();) {
				SelectionKey key = keyIter.next();
				keyIter.remove();
				System.out.println(key.readyOps());
				if (key.isAcceptable()) {
					System.out.println(&quot;Accept&quot;);
					// 接受连接到此Channel的连接
					socketChannel.accept();
				}
			}
		}
	}
}
</pre>
	</div>
	<div style="text-align: -webkit-auto; ">
		<span style="text-align: -webkit-auto; ">Selector客户端样例代码：</span></div>
	<div style="text-align: -webkit-auto; ">
		<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
/**
 * Java NIO Selector模式，客户端代码
 * 
 * @author lihzh
 * @alia OneCoder
 * @blog http://www.coderli.com
 */
public class NioSelectorClient {

	/**
	 * @author lihzh
	 * @throws IOException 
	 * @alia OneCoder
	 */
	public static void main(String[] args) throws IOException {
		SocketChannel channel = SocketChannel.open();
		channel.configureBlocking(false);
		channel.connect(new InetSocketAddress(&quot;127.0.0.1&quot;, 8000));
	}
}
</pre>
	</div>
	<div style="text-align: -webkit-auto; ">
		<div style="text-align: -webkit-auto; ">
			代码很简单，服务端接受到客户端的连接请求后，会打印出&quot;Accept&quot;信息。</div>
		<div style="text-align: -webkit-auto; ">
			&nbsp;</div>
		<div style="text-align: -webkit-auto; ">
			简单概括就是，整一个通道，通道加个选择过滤器，看来的事件是不是我想要的，不想要的干脆不管，想要的，我就存起来，留着慢慢处理。</div>
		<div style="text-align: -webkit-auto; ">
			&nbsp;</div>
		<div style="text-align: -webkit-auto; ">
			现在感觉是不是Netty确实跟这个机制比较想，如果让你去实现Netty先有的功能，也有思路可想了吧。</div>
	</div>
</div>
<p>
	&nbsp;</p>

