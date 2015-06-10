---
layout: post
title: Java NIO框架Netty教程（三）- 字符串消息收发
date: 2012-07-12 21:18
author: onecoder
comments: true
categories: [ChannelBuffer, Netty, Netty, 分布式]
---
<p>
	<span style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">了解了Netty的</span><a href="http://www.coderli.com/archives/netty-two-concepts/" style="cursor: pointer; font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">基本概念</a><span style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">，开发起来应该会顺手很多。&nbsp;&nbsp;在</span><a href="http://www.coderli.com/archives/netty-course-hello-world/" style="cursor: pointer; font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">&ldquo;Hello World&rdquo;</a><span style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">代码中，我们只是在完成绑定的时候，在各自的本地打印了简单的信息，并没有客户端和服务端的消息传递。这个肯定是最基本的功能。在上代码之前，先补充一个Netty中重要的概念，ChannelBuffer。</span></p>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	&nbsp;</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	&nbsp;</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	&nbsp;</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	&nbsp;</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	&nbsp;</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	&nbsp;</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	<ul>
		<li>
			<b style="font-size: 22px; ">ChannelBuffer</b></li>
	</ul>
	<div style="text-align: center; ">
		<img alt="ChannelBuffer" src="http://onecoder.qiniudn.com/8wuliao/C6PXEavo/CT0dH.jpg" style="width: 600px; height: 149px;" /><br />
		&nbsp;</div>
	<div>
		<span style="text-align: -webkit-auto; ">&nbsp;Netty中的消息传递，都必须以字节的形式，以ChannelBuffer为载体传递。简单的说，就是你想直接写个字符串过去，对不起，抛异常。虽然，Netty定义的writer的接口参数是Object的，这可能也是会给新上手的朋友容易造成误会的地方。Netty源码中，是这样判断的：</span><br />
		<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
SendBuffer acquire(Object message) {
        if (message instanceof ChannelBuffer) {
            return acquire((ChannelBuffer) message);
        } else if (message instanceof FileRegion) {
            return acquire((FileRegion) message);
        }

        throw new IllegalArgumentException(
                &quot;unsupported message type: &quot; + message.getClass());
    }
</pre>
	</div>
</div>
<p>
	<span style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">所以，我们要想传递字符串，那么就必须转换成ChannelBuffer。明确了这一点，接下来我们上代码：</span></p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
/**
 * @author lihzh
 * @alia OneCoder
 * @blog http://www.coderli.com
 */
public class MessageServer {

	public static void main(String args[]) {
		// Server服务启动器
		ServerBootstrap bootstrap = new ServerBootstrap(
				new NioServerSocketChannelFactory(
						Executors.newCachedThreadPool(),
						Executors.newCachedThreadPool()));
		// 设置一个处理客户端消息和各种消息事件的类(Handler)
		bootstrap.setPipelineFactory(new ChannelPipelineFactory() {
			@Override
			public ChannelPipeline getPipeline() throws Exception {
				return Channels.pipeline(new MessageServerHandler());
			}
		});
		// 开放8000端口供客户端访问。
		bootstrap.bind(new InetSocketAddress(8000));
	}

	private static class MessageServerHandler extends SimpleChannelHandler {

		/**
		 * 用户接受客户端发来的消息，在有客户端消息到达时触发
		 * 
		 * @author lihzh
		 * @alia OneCoder
		 */
		@Override
		public void messageReceived(ChannelHandlerContext ctx, MessageEvent e) {
			ChannelBuffer buffer = (ChannelBuffer) e.getMessage();
			System.out.println(buffer.toString(Charset.defaultCharset()));
		}

	}
}
</pre>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
/**
 * @author lihzh
 * @alia OneCoder
 * @blog http://www.coderli.com
 */
public class MessageClient {

	public static void main(String args[]) {
		// Client服务启动器
		ClientBootstrap bootstrap = new ClientBootstrap(
				new NioClientSocketChannelFactory(
						Executors.newCachedThreadPool(),
						Executors.newCachedThreadPool()));
		// 设置一个处理服务端消息和各种消息事件的类(Handler)
		bootstrap.setPipelineFactory(new ChannelPipelineFactory() {
			@Override
			public ChannelPipeline getPipeline() throws Exception {
				return Channels.pipeline(new MessageClientHandler());
			}
		});
		// 连接到本地的8000端口的服务端
		bootstrap.connect(new InetSocketAddress(&quot;127.0.0.1&quot;, 8000));
	}

	private static class MessageClientHandler extends SimpleChannelHandler {

		/**
		 * 当绑定到服务端的时候触发，给服务端发消息。
		 * 
		 * @alia OneCoder
		 * @author lihzh
		 */
		@Override
		public void channelConnected(ChannelHandlerContext ctx,
				ChannelStateEvent e) {
			// 将字符串，构造成ChannelBuffer，传递给服务端
			String msg = &quot;Hello, I&#39;m client.&quot;;
			ChannelBuffer buffer = ChannelBuffers.buffer(msg.length());
			buffer.writeBytes(msg.getBytes());
			e.getChannel().write(buffer);
		}
	}

}
</pre>
<p>
	<span style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">与&nbsp;</span><a href="http://www.coderli.com/archives/netty-course-hello-world/" style="cursor: pointer; font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">&ldquo;Hello World&rdquo;</a><span style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">&nbsp;样例代码不同的是，客户端在channel连通后，不是在本地打印，而是将消息转换成ChannelBuffer传递给服务端，服务端接受到ChannelBuffer后，解码成字符串打印出来。</span></p>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	<br />
	<div>
		同时，通过对比可以发现，变动的只是Handler里的代码，启动服务和绑定服务的代码没有变化，也就是我们在<a href="http://www.coderli.com/archives/netty-two-concepts/" style="cursor: pointer; ">概念介绍</a>里提到了，关注Handler，在Handler里处理我们自己的业务。所以，以后我们会只给出业务中关键代码，不会在上重复的代码：）</div>
</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	&nbsp;</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	由于在Netty中消息的收发全依赖于ChannelBuffer，所以，下一章我们将会详细的介绍ChannelBuffer的使用。我们一起学习。</div>

