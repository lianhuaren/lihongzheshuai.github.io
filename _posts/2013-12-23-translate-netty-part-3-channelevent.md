---
layout: post
title: Netty教程—Part3—ChannelEvent—真的吗？ 
date: 2013-12-23 00:25
author: onecoder
comments: true
categories: [Java, Netty, Netty]
---
<div>
	本文是Netty教程的第三篇。</div>
<div>
	&nbsp;</div>
<div>
	ChannelEvent不 仅仅包含ChannelBuffer。在Netty几乎所有的东西都是异步的，代码驱动模式中有些代码用来生成事件（例如连接、断开连接、写入），有些代码是在ChannelFactory线程池中执行时处理这些事件。在ChannelEvent的文档中有一个全面的列表介绍所有的事件及区别。事件处理器比较好的例子可以参考SimpleChannelHandler的文档。这个处理器实现类几乎为每种类型的事件都提供了方法。甚至还有一个IdleStateEvent事件，当一个Channel处于空闲状态会触发这个事件，非常有用。</div>
<div>
	&nbsp;</div>
<div>
	为了实现一个不仅仅能支持简单的ChannelBuffer发送器/接收器，我们需要在Pipeline中添加ChannelHandler。通常情况下，ChannelHandler用作编码器和解码器，功能如下：</div>
<div>
	&nbsp;</div>
<ul>
	<li>
		&nbsp;编码器：将一个非ChannelBuffer类 型的对象转换为适合传送到其他地方的ChannelBuffer。 这个对象或许不会直接被转换为ChannelBuffer，而是部分的被转化，然后由Pipeline中的其他处理器来完成整个流程。不管怎样，如果你不是直接发送ChannelBuffer数 据，那么Pipeline中的处理器会在这个数据被发送出去之前协同将它转换为ChannelBuffer。ObjectEncoder就是一个例子，它将普通的可序列化的Java对 象转换为ChannelBuffer中表示对象的字节。</li>
	<li>
		解码器： 编码器的反向流程，将ChannelBuffer中的内容转换为有用的数据。ObjectEncoder对 应的解码器就是ObjectDecoder。</li>
</ul>
<div>
	Netty SDK提供了不同种类的编解码器，例如Google ProtoBufs、Compression、Http Request/Response和Base64。</div>
<div>
	&nbsp;</div>
<div>
	并不是所有的ChannelHandler都是编码器/解码器（尽管核心API中的大多数都是）。ChannelHandler可以用来做各种有用的事情。例如：</div>
<div>
	&nbsp;</div>
<ul>
	<li>
		ExecutionHandler： 将ChannelEvent转发给其他线程池。将事件从Worker（I/O 处 理）线程池中踢出来保证它一直有效。</li>
	<li>
		BlockingReadHandler：允许你的代码将一个Channel以非异步的方式来读取传入数据。</li>
	<li>
		LoggingHandler：仅仅记录当前哪些事件经过这个处理器了，是非常有用的调试工具&hellip;&hellip;</li>
</ul>
<div>
	那么我应该如何使用ChannelHandler来帮助我处理java.util.Data问题呢？答案是你需要在Channel的Pipeline中添加一个ObjectEncoder，它可以将Date转义为ChannelBuffer。下面展示了流程图，稍后会讲解代码：</div>
<div style="text-align: center;">
	<img alt="" src="http://onecoder.qiniudn.com/8wuliao/DpaRB9vK/e73WS.png" /></div>
<div>
	&nbsp;</div>
<div>
	ObjectEncoder是Netty最好的特性之一。只要你在Pipeline的另一端使用Netty ObjectEncoder就能顺利的执行。如果不可以，你需要使用基于Java的标准序列化编码器CompatibleObjectEncoder 。</div>
<div>
	&nbsp;</div>
<div>
	新的关注点：我们需要一个Channel，可以从ChannelFactory获取，此外还需要定义一个 ChannelPipeline。 有一些方式能够做到这些，但是Netty提 供了一个Bootstrap构建器能够将所有的构件很漂亮的包装在一起。下面展示了这些模块如何组装在一起，创建一个Netty客户端来发送Date。这个示例要实现 NioClientSocketChannelFactory来创建一个NioClientSocketChannel实例。顺便说一句，在大多数情况下Netty公共API只 是简明地返回一个SocketChannel、一个抽象的父类实例。你可以看作一个简单的Channel，实现了他们本身公共的相同行为和公开的功能。和你在其他代码中看到的相比，这个代码可能有些简单，但是我已经讲解的很清楚了。</div>
<div>
	&nbsp;</div>
<div>
	<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
Executor bossPool = Executors.newCachedThreadPool();
Executor workerPool = Executors.newCachedThreadPool();
ChannelFactory channelFactory = new NioClientSocketChannelFactory(bossPool, workerPool);
ChannelPipelineFactory pipelineFactory = new ChannelPipelineFactory() {
  public ChannelPipeline getPipeline() throws Exception {
    return Channels.pipeline(
      new ObjectEncoder()
    }
};
Bootstrap boostrap = new ClientBootstrap(channelFactory);
boostrap.setPipelineFactory(pipelineFactory);
// Phew. Ok. We built all that. Now what ?
InetSocketAddress addressToConnectTo = new InetSocketAddress(remoteHost, remotePort);
ChannelFuture cf = bootstrap.connect(addressToConnectTo);
</pre>
</div>
<div>
	这就是如何获取一个Channel。实际上这是一个ChannelFuture。 在Netty中几乎所有的操作都是异步的，当你需要一个连接，创建这个连接的实际流程是异步的。因此，Bootstrap返 回一个ChannelFuture，用于处理连接创建完成的事件。ChannelFuture提供请求处理的状态,假定连接成功创建,并且提供一个Channel。 调用线程有多种方式等待连接创建完成，因为基于channels的异步操作（断开连接、写操作等）也返回ChannelFuture，这些方式也适用于这些操作，下面列出了这些方式：</div>
<div>
	&nbsp;</div>
<div>
	【A】 &nbsp;等待戈多（戈多是指一次Channel请求）</div>
<div>
	&nbsp;</div>
<div>
	不考虑等待Channel操作完成的实现策略，操作完成并不意味着操作成功，简单来说，完成时的结果有如下几种类型：</div>
<div>
	&nbsp;</div>
<ul>
	<li>
		成功：操作成功完成。</li>
	<li>
		失败：操作失败，可以通过ChannelFuture.getCause()来获取失败的原因(异常)。</li>
	<li>
		超时：需要考虑的一种失败结果。</li>
	<li>
		取消：调用ChannelFuture.cancle()来取消channel请 求。</li>
</ul>
<div>
	话已至此，下面是如何等待操作完成：</div>
<div>
	&nbsp;</div>
<ul>
	<li>
		等待（Await）：Await是wait的另一种说法。(或许是为了避免和Object.wait()混淆？)ChannelFuture提供了一些方法是连接线程一直等待直到连接操作完成。注意这并不意味这线程会wait直到连接成功被创建，而在连接创建失败、创建超时、被取消的时候也会结束等待状态。如果感觉内容太多，可以跳过下面的章节，异步操作需要有不同类型的wait方式，但基本分为如下类型：</li>
	<li>
		Interruptibility：处于wait状态的线程可以被中止。这是线程生 命周期的本质特性，当一个线程被置为wait状态（sleep、join等）时，Java线程API一定会抛出一个需要处理的异常（InterruptedException）。然而ChannelFuture提供了一个不需要try/catch包装的调用方法awaitUninterruptibly，这个方法会忽略interrupt()调用。如果需要可中断的方法，则调用await。</li>
	<li>
		Timeout：因为各种原因，操作可能因不明原因一直被阻塞，明智的做法是在Channel操作上设置超时时间，可以保证在抛出超时异常之前，线程能够一直处于wait状态。</li>
	<li>
		不等待：连接线程可以通过在ChannelFuture上注册一个监听器来实现自动引导模式，在操作完成后这个监听器会被触发。监听器需要实现ChannelFutureListener接口，操作完成之后会将ChannelFuture座位入参回调这个接口。</li>
</ul>
<div>
	总之，下面的示例是关于wait种类的，使用连接操作做为唤醒wait的异步事件，同样也适用于大多数操作：</div>
<div>
	&nbsp;</div>
<div>
	<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
// Waiting on a connect. (Pick one)
ChannelFuture cf = bootstrap.connect(addressToConnectTo);
// A. wait interruptibly
cf.await();
// B. wait interruptibly with a timeout of 2000 ms.
cf.await(2000, TimeUnit.MILLISECONDS);
// C. wait uninterruptibly
cf.awaitUninterruptibly();
// D. wait uninterruptibly with a timeout of 2000 ms.
cf.awaitUninterruptibly(2000, TimeUnit.MILLISECONDS);
// E. add a ChannelFutureListener that writes the Date when the connect is complete
cf.addListener(new ChannelFutureListener(){
 public void operationComplete(ChannelFuture future) throws Exception {
  // chek to see if we succeeded
  if(future.isSuccess()) {
   Channel channel = future.getChannel();
   channel.write(new Date());
   // remember, the write is asynchronous too !
  }
 }
});
// if a wait option was selected and the connect did not fail,
// the Date can now be sent.
Channel channel = cf.getChannel();
channel.write(new Date());
</pre>
</div>
<div>
	因为你的客户端线程在等待的过程中可能不会有太多操作，awaitXXX很适合用于客户端应用。在服务器代码中（或许是一些代理服务器），因为很可能会有大量的任务在排队等待处理，线程不应该等待IO事件完成，更好的选择是连接事件的回调。</div>
<div>
	&nbsp;</div>
<div>
	在Netty文档说明的很清晰， 但依然值得在这里强调：为了保证你的工作线程执行而不是等待，需要避免在工作线程中调用任何await方法。</div>
<div>
	&nbsp;</div>
<div>
	连接创建完成最后一个或几个步骤是触发一些有用的操作。在上面的示例中，当链接成功创建 后会注册一个ChannelFutureEvent，并且在监听器中执行写操作。此处不清晰的地方当ChannelStateEvent广播的时候operationComplete方法会被回调。ChannelStateEvent是个ChannelEvent的一个实例，当前Channel状态修改后，会广播这个事件。ChannelFuture会调用一个预定义的回调接口处理这个事件，但是ChannelEvent会被传送到pipeline中的所有处理器中，所以你可以在某个处理器中实现连接创建后的操作。我之所以在这里指出这个流程是因为Netty大多示例代码实现方式很不直观。例如，ObjectEchoClient类完成了创建一个Channel所有的任务，却没有任何写入操作。实际的对象回写操作是在pipeline（ObjectEchoClientHandler）中最后一个处理器完成的，当处理器接收表示连接已创建的ChannelSateEvent事件后会被执行。</div>
<div>
	&nbsp;</div>
<div>
	<span style="color: rgb(25, 25, 25); font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px; background-color: rgb(251, 251, 251);">原文链接：&nbsp;</span><a class="external" href="http://seeallhearall.blogspot.com/2012/05/netty-tutorial-part-1-introduction-to.html" rel="nofollow" style="margin: 0px; padding: 0px; font-size: 14px; vertical-align: baseline; background-color: rgb(251, 251, 251); outline: none; text-decoration: none; border-color: rgb(8, 155, 195); color: rgb(8, 155, 195); font-family: 微软雅黑, Verdana, sans-serif, 宋体; line-height: 22px;" target="_blank">seeallhearall.blogspot</a><span style="color: rgb(25, 25, 25); font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px; background-color: rgb(251, 251, 251);">&nbsp;翻译：&nbsp;</span><a href="http://www.importnew.com/" style="margin: 0px; padding: 0px; font-size: 14px; vertical-align: baseline; background-color: rgb(251, 251, 251); outline: none; text-decoration: none; border-color: rgb(8, 155, 195); color: rgb(8, 155, 195); font-family: 微软雅黑, Verdana, sans-serif, 宋体; line-height: 22px;">ImportNew.com&nbsp;</a><span style="color: rgb(25, 25, 25); font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px; background-color: rgb(251, 251, 251);">-&nbsp;</span><a href="http://www.importnew.com/author/liuhaibo" style="margin: 0px; padding: 0px; font-size: 14px; vertical-align: baseline; background-color: rgb(251, 251, 251); outline: none; text-decoration: none; border-color: rgb(8, 155, 195); color: rgb(8, 155, 195); font-family: 微软雅黑, Verdana, sans-serif, 宋体; line-height: 22px;">刘海波</a><br style="color: rgb(25, 25, 25); font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px; background-color: rgb(251, 251, 251);" />
	<span style="color: rgb(25, 25, 25); font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px; background-color: rgb(251, 251, 251);">译文链接：&nbsp;</span><a href="http://www.importnew.com/7679.html" style="margin: 0px; padding: 0px; font-size: 14px; vertical-align: baseline; background-color: rgb(251, 251, 251); outline: none; text-decoration: none; border-color: rgb(8, 155, 195); color: rgb(8, 155, 195); font-family: 微软雅黑, Verdana, sans-serif, 宋体; line-height: 22px;">http://www.importnew.com/7679.html</a></div>
<div>
	<br style="color: rgb(25, 25, 25); font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 16px; font-weight: bold; line-height: 22px; background-color: rgb(251, 251, 251);" />
	<img src="http://www.importnew.com/importnew-weixin.jpg" style="margin: 0px; padding: 0px; border: 0px; outline: 0px; font-size: 16px; vertical-align: baseline; background-color: rgb(251, 251, 251); color: rgb(25, 25, 25); font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-weight: bold; line-height: 22px;" /></div>

