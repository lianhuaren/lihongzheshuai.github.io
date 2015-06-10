---
layout: post
title: 国外Netty教程翻译—Part5—Server端返回日期数据 
date: 2013-12-25 23:52
author: onecoder
comments: true
categories: [Java, Netty, Netty]
---
<p>
	本文是Netty教程的第五篇。<br />
	<br />
	客户端和服务器端都需要双向的读写操作。例如DateSender示 例，如果服务器端想将日期数据增加任意的天数，然后将修改后的日期返回，应该如何做呢？有个代码几乎和DateSender相 同的示例：DateModifier ，这个示例的服务器端 修改日期然后将修改后的数据返 回给客户端。为了实现这个功能，需要做如下修改：</p>
<ol>
	<li>
		&nbsp;&nbsp;&nbsp; 服务器端需要在pipeline中添加一个ObjectEncoder， 才能返回一个日期数据给客户端。</li>
	<li>
		&nbsp;&nbsp;&nbsp; 客户端接收从服务器端返回的日期数据需要在pipeline中添加ObjectDecorder。</li>
	<li>
		&nbsp;&nbsp;&nbsp; 客户端需要添加一个额外的处理器来利用接收到的数据完成一些逻辑。</li>
</ol>
<p>
	<br />
	下面代码创建了新客户端的pipeline：<br />
	&nbsp;&nbsp;</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
ChannelPipelineFactory pipelineFactory = new ChannelPipelineFactory() {
  public ChannelPipeline getPipeline() throws Exception {
    return Channels.pipeline(
      new ObjectEncoder(),
      // Next 2 Lines are new
      new ObjectDecoder(ClassResolvers.cacheDisabled(getClass().getClassLoader())),
      new ClientDateHandler()
    );
  }
};

</pre>
<p>
	客户端的处理器DateHandler非常简单，当客户端接收数据并且将数据解码之后会调用这个处理器。</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
static class ClientDateHandler extends SimpleChannelHandler {
 public void messageReceived(ChannelHandlerContext ctx,MessageEvent e) throws Exception {
  Date date = (Date)e.getMessage();
  clog(&quot;Hey Guys !  I got back a modified date ! [&quot; + date + &quot;]&quot;);
 }
}

</pre>
<p>
	服务器端的pipeline几乎和客户端一样：</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
bootstrap.setPipelineFactory(new ChannelPipelineFactory() {
 public ChannelPipeline getPipeline() throws Exception {
  return Channels.pipeline(
   new ObjectDecoder(ClassResolvers.cacheDisabled(getClass().getClassLoader())),
   // Next 2 Lines are new
   new ObjectEncoder(),
   new ServerDateHandler()
  );
 };
});

</pre>
<p>
	下面的代码是新的服务器端日期处理器DateHandler，里面有一些新的代码：&nbsp;&nbsp;&nbsp;</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
static class ServerDateHandler extends SimpleChannelHandler {
 Random random = new Random(System.nanoTime());
 public void messageReceived(ChannelHandlerContext ctx,MessageEvent e) throws Exception {
  Date date = (Date)e.getMessage();
  // Here&#39;s the REALLY important business service at the end of the pipeline
  long newTime = (date.getTime() + random.nextInt());
  Date newDate = new Date(newTime);
  slog(&quot;Hey Guys !  I got a date ! [&quot; + date + &quot;] and I modified it to [&quot; + newDate + &quot;]&quot;);
  // Send back the reponse
  Channel channel = e.getChannel();
  ChannelFuture channelFuture = Channels.future(e.getChannel());
  ChannelEvent responseEvent = new DownstreamMessageEvent(channel, channelFuture, newDate, channel.getRemoteAddress());
  ctx.sendDownstream(responseEvent);
  // But still send it upstream because there might be another handler
  super.messageReceived(ctx, e);
 }
}

</pre>
<p>
	在第10-13行是一些新代码基于一个精妙的理念，主要用于将修改后的日期返回给调用的客户端。</p>
<ol>
	<li>
		&nbsp;&nbsp;&nbsp; 在第10行，从MessageEvent中获取了一个Channel的引用。</li>
	<li>
		&nbsp;&nbsp;&nbsp; 日期数据即将被异步(会出 现重复调用写操作的风险)写回客户端，因此总会调用ChannelFuture。在第11行，使用Channels创建一个ChannelFuture对象，Channels有 很多有用的类似future的静态方法。</li>
	<li>
		&nbsp;&nbsp;&nbsp; 第12行创建一个新MessageEvent：DownstreamMessageEvent。基本 上，返回值已经被打包准备回传给客户端。</li>
	<li>
		&nbsp;&nbsp;&nbsp; 第13行调用ChannelHandlerContext的sendDownstream方法将MessageEvent发送给客户端。</li>
</ol>
<p>
	<br />
	有什么需要注意的吗？ChannelHandlerContext基本上是pipeline功能上的引用。它能和pipeline一样 访问所有的处理器。更重要的是，它提供了一种和接收信息一样的路径来将数据发送回去。通过如下方法实现：</p>
<ul>
	<li>
		&nbsp;&nbsp;&nbsp; 注意，在pipeline中除了ServerDateHandler之外可能还有其他的处理器，希望响应客户端将修改后的日期数据发送回去。然后，将当前接收的数据继续传送到pipeline中的下一个处理器。</li>
	<li>
		&nbsp;&nbsp;&nbsp; 如果直接将返回的日期数据直接写到Channel中，日期数据会从pipeline的上层开始推送到某些不期望被调用的处理器中。</li>
</ul>
<p>
	<br />
	所有这些东西引出了一 些额外的细节：pipeline中的处理器如何处理工作的，并解释了Upstream和Downstream。</p>
<p>
	&nbsp;</p>
<p>
	原文链接： <a class="external" href="http://seeallhearall.blogspot.com/2012/05/netty-tutorial-part-1-introduction-to.html" rel="nofollow" target="_blank">seeallhearall.blogspot</a> 翻译： <a href="http://www.importnew.com"> ImportNew.com </a> - <a href="http://www.importnew.com/author/liuhaibo">刘海波</a><br />
	译文链接： <a href="http://www.importnew.com/7692.html">http://www.importnew.com/7692.html</a></p>
<p>
	<span style="font-size: 16px;font-weight: bold;"><img src="http://www.importnew.com/importnew-weixin.jpg" /></span></p>

