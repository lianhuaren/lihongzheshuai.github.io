---
layout: post
title: Java NIO框架Netty教程(十二)-并发访问测试(中)
date: 2012-08-20 22:02
author: onecoder
comments: true
categories: [Netty, Netty, nio, 并发]
---
<p>
	写在前面：对Netty并发问题的测试和解决完全超出了我的预期，想说的东西越来越多。所以才出现这个中篇，也就是说，一定会有下篇。至于问题点的发现，<a href="http://www.coderli.com">OneCoder</a>也在努力验证中。</p>
<p>
	继续并发的问题。在<a href="http://www.coderli.com/archives/netty-concurrency-problem-one/">《Java NIO框架Netty教程(十一)-并发访问测试(上)》</a>中，我们测试的其实是一种伪并发的情景。底层实际只有一个Channel在运作，不会出现什么无响应的问题。而实际的并发不是这个意思的，独立的客户端，自然就会有独立的channel。也就是一个服务端很可能和很多的客户端建立关系，就有很多的Channel，进行了多次的绑定。下面我们来模拟一下这种场景。</p>
<pre class="brush:java;first-line:1;pad-line-numbers:false;highlight:null;collapse:false;">
/**
 * Netty并发，多connection，socket并发测试
 * 
 * @author lihzh(OneCoder)
 * @alia OneCoder
 * @Blog http://www.coderli.com
 * @date 2012-8-13 下午10:47:28
 */
public class ConcurrencyNettyMultiConnection {
	
	public static void main(String args[]) {
		final ClientBootstrap bootstrap = new ClientBootstrap(
				new NioClientSocketChannelFactory(
						Executors.newCachedThreadPool(),
						Executors.newCachedThreadPool()));
		// 设置一个处理服务端消息和各种消息事件的类(Handler)
		bootstrap.setPipelineFactory(new ChannelPipelineFactory() {
			@Override
			public ChannelPipeline getPipeline() throws Exception {
				return Channels.pipeline(new ObjectEncoder(),
						new ObjectClientHandler()
				);
			}
		});
		for (int i = 0; i &lt; 3; i++) {
	            bootstrap.connect(new InetSocketAddress(&quot;127.0.0.1&quot;, 8000));
		}
	}
}
</pre>
<p>
	看到这段代码，你可能会疑问，这是在做什么。这个验证是基于酷壳的文章<a href="http://blog.csdn.net/haoel/article/details/2224055">《Java NIO类库Selector机制解析（上）》</a>。他是基于Java Selector直接进行的验证，Netty是在此之上封装了一层。其实<a href="http://www.coderli.com">OneCoder</a>也做了Selector层的验证。</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
/**
 * Selector打开端口数量测试
 * 
 * @author lihzh
 * @alia OneCoder
 * @blog http://www.coderli.com
 */
public class ConcurrencySelectorOpen {

	/**
	 * @author lihzh
	 * @alia OneCoder
	 * @throws IOException 
	 * @throws InterruptedException 
	 * @date 2012-8-15 下午1:57:56
	 */
	public static void main(String[] args) throws IOException, InterruptedException {
		for (int i = 0; i &lt; 3; i++) {
			Selector selector = Selector.open();
			SocketChannel channel = SocketChannel.open();
			channel.configureBlocking(false);
			channel.connect(new InetSocketAddress(&quot;127.0.0.1&quot;, 8000));
			channel.register(selector, SelectionKey.OP_READ);
		}
		Thread.sleep(300000);
	}
}

</pre>
<p>
	同样，通过Process Explorer去观察端口占用情况，开始跟酷壳大哥的观察到的效果一样。当不启动Server端，也就是不实际跟Server端建立链接的时候，3次selector open，占用了6个端口。</p>
<p style="text-align: center; ">
	<img alt="" src="http://onecoder.qiniudn.com/8wuliao/CcLVlU9W/7FrMP.jpg" /></p>
<p>
	当启动Server端，进行绑定的时候，占用了9个端口</p>
<p style="text-align: center; ">
	<img alt="" src="http://onecoder.qiniudn.com/8wuliao/CcLVm0dr/BI25e.jpg" /></p>
<p>
	其实，多的三个，就是实际绑定的8000端口。其余就是酷壳大哥说的内部Loop链接。也就是说，对于我们实际场景，一次链接需要的端口数是3个。操作系统的端口数和资源当然有有限的，也就是说当我们增大这个链接的时候，错误必然会发生了。<a href="http://www.coderli.com">OneCoder</a>尝试一下3000次的场景，并没有出现错误，不过这么下去出错肯定是必然的。</p>
<p>
	再看看服务端的情况，</p>
<p style="text-align: center; ">
	<img alt="" src="http://onecoder.qiniudn.com/8wuliao/CcLVmpqk/IE40R.jpg" style="width: 443px; height: 517px; " /></p>
<p>
	这个还是直接通过Selector连接的时候的服务端的情况，除了两个内部回环端口以外，都是通过监听的8000的端口与外界通信，所以，服务端不会有端口限制的问题。不过，也可以看到，如果对链接不控制，服务端也维持大量的连接耗费系统资源！所以，良好的编码是多么的重要。</p>
<p>
	回到我们的主题，Netty的场景。先使用跟Selector测试一样的场景，单线程，一个bootstrap，连续多次connect看看错误和端口占用的情况。代码也就是开头提供的那段代码。</p>
<p>
	看看测试结果，</p>
<p style="text-align: center; ">
	<img alt="" src="http://onecoder.qiniudn.com/8wuliao/CcLVmPCQ/pg6Ed.jpg" /></p>
<p>
	同样连接后还是占用9个端口。如果手动调用了channle.close()方法，则会释放与8000链接的端口，也就是变成6个端口占用。</p>
<p>
	增大连续连接数到10000。</p>
<p>
	首先没有报错，在每次close channel情况下，客户端端口占用情况如图（服务端情况类似）。</p>
<p style="text-align: center; ">
	<img alt="" src="http://onecoder.qiniudn.com/8wuliao/CcMcO5AL/9Fqan.jpg" /></p>
<p>
	可见，并没有像selector那样无限的增加下去。这正是Netty中worker的巨大作用，帮我们管理这些琐碎的链接。具体分析，我们留待下章，您也可以自己研究一下。(<a href="http://www.coderli.com">OneCoder</a>注：如果没关闭channel，会发现对8000端口的占用，会迅速增加。系统会很卡。)</p>
<p>
	调整测试代码，用一个线程来模拟一个客户端的请求。</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
/**
 * Netty并发，多connection，socket并发测试
 * 
 * @author lihzh(OneCoder)
 * @alia OneCoder
 * @Blog http://www.coderli.com
 * @date 2012-8-13 下午10:47:28
 */
public class ConcurrencyNettyMultiConnection {
	
	public static void main(String args[]) {
		final ClientBootstrap bootstrap = new ClientBootstrap(
				new NioClientSocketChannelFactory(
						Executors.newCachedThreadPool(),
						Executors.newCachedThreadPool()));
		// 设置一个处理服务端消息和各种消息事件的类(Handler)
		bootstrap.setPipelineFactory(new ChannelPipelineFactory() {
			@Override
			public ChannelPipeline getPipeline() throws Exception {
				return Channels.pipeline(new ObjectEncoder(),
						new ObjectClientHandler()
				);
			}
		});
		for (int i = 0; i &lt; 1000; i++) {
			// 连接到本地的8000端口的服务端
			Thread t = new Thread(new Runnable() {
				@Override
				public void run() {
					bootstrap.connect(new InetSocketAddress(&quot;127.0.0.1&quot;, 8000));
					try {
						Thread.sleep(300000);
					} catch (InterruptedException e) {
						e.printStackTrace();
					}
				}
			});
			t.start();
		}
	}
}
</pre>
<p>
	不出所料，跟微博上问我问题的朋友出现的问题应该是一样的：</p>
<blockquote>
	<p>
		Write: 973<br />
		Write: 974<br />
		八月 17, 2012 9:57:28 下午 org.jboss.netty.channel.SimpleChannelHandler<br />
		警告: EXCEPTION, please implement one.coder.netty.chapter.eight.ObjectClientHandler.exceptionCaught() for proper handling.<br />
		java.net.ConnectException: Connection refused: no further information</p>
</blockquote>
<p>
	这个问题，笔者确实尚未分析出原因，仅从端口占用情况来看，跟前面的测试用例跑出的效果基本类似。应该说明不是端口不足导致的，不过<a href="http://www.coderli.com">OneCoder</a>尚不敢妄下结论。待研究后，我们下回分解吧：）您有任何想法，也可以提供给我，我来进行验证。</p>

