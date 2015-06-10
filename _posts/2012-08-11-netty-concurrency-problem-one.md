---
layout: post
title: Java NIO框架Netty教程(十一)-并发访问测试(上)
date: 2012-08-11 23:11
author: onecoder
comments: true
categories: [Netty, Netty, nio, 并发]
---
<p>
	之前更新了几篇关于JVM研究的文章，其实也是在做本篇文章验证的时候，跑的有点远，呵呵。回归Netty教程，这次要讲的其实是针对一个问题的研究和验证结论。另外，最近工作比较忙，所以可能会分文章更新一些阶段性的成果，而不是全部汇总更新，以免间隔过久。</p>
<p>
	起因是一个朋友，通过微博(<a href="http://t.qq.com/lihongzheshuai">OneCoder腾讯微博</a>、<a href="http://weibo.com/kubicoder">OneCoder新浪微博</a>、<a href="http://t.163.com/onecoder">OneCoder网易微博</a>、<a href="http://onecoder.t.sohu.com/">OneCoder搜狐微博</a>)私信给我一个问题，大意是说他在用Netty做并发测试的时候，会出现大量的connection refuse信息，问我如何解决。</p>
<p>
	没动手就没有发言权，所以<a href="http://www.coderli.com">OneCoder</a>决定测试一下：</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
/**
 * @author lihzh
 * @alia OneCoder
 * @blog http://www.coderli.com
 */
public class ConcurrencyNettyTestHandler extends SimpleChannelHandler {

	private static int count = 0;

	/**
	 * 当接受到消息的时候触发
	 */
	@Override
	public void channelConnected(ChannelHandlerContext ctx,
			final ChannelStateEvent e) throws Exception {
		for (int i = 0; i &lt; 100000; i++) {
			Thread t = new Thread(new Runnable() {
				@Override
				public void run() {
					sendObject(e.getChannel());
				}
			});
			System.out.println(&quot;Thread count: &quot; + i);
			t.start();
		}

	}

	/**
	 * 发送Object
	 * 
	 * @author lihzh
	 * @alia OneCoder
	 */
	private void sendObject(Channel channel) {
		count++;
		Command command = new Command();
		command.setActionName(&quot;Hello action.&quot;);
		System.out.println(&quot;Write: &quot; + count);
		channel.write(command);
	}
}
</pre>
<p>
	运行结果：</p>
<blockquote>
	<p>
		Hello action.: 99996<br />
		Hello action.: 99997<br />
		Hello action.: 99998<br />
		Hello action.: 99999<br />
		Hello action.: 100000</p>
</blockquote>
<p>
	你可能会惊讶，10w个请求都能通过？呵呵，细心的同学，可能会发现，这其实并不是并发，而只是所谓10w个线程的，单channel的伪并发，或者说是一种持续的连续访问。并且，如果你跑一下测试用例，会发现，Server端开始接受处理消息，是在Client端10w个线程请求都结束之后再开始的。这是为什么？</p>
<p>
	其实，如果您看过<a href="http://www.coderli.com">OneCoder</a>的《<a href="http://www.coderli.com/archives/netty-message-receive-count-mismatch-two/">Java NIO框架Netty教程(七)-再谈收发信息次数问题</a>》，应该会有所联想。不过坦白的说，<a href="http://www.coderli.com">OneCoder</a>也是在经过一番周折，一番Debug以后，才发现了这个问题。当<a href="http://www.coderli.com">OneCoder</a>在线程内断点以后，放过一个线程，接收端就会有一条信息出现，这其实是和之前文章里介绍的场景是一样的。所以，呵呵，可能对您来说，看了这篇文章，并没有更多的收获，但是对<a href="http://www.coderli.com">OneCoder</a>来说，确实是经历了不小的周折，绕了挺大的弯子，也算是对代码的再熟悉过程吧。</p>
<p>
	下篇我们会面对真正并发的问题：）</p>

