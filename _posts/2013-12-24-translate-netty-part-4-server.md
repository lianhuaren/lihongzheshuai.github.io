---
layout: post
title: 国外Netty教程翻译—Part4—Server端的执行情况
date: 2013-12-24 00:01
author: onecoder
comments: true
categories: [Java, Netty, Netty]
---
<div>
	本文是Netty教程的第四篇。</div>
<div>
	&nbsp;</div>
<div>
	根据DateSender，我们 只知道服务器端socket是用来监听客服端socket发送的数据。服务器端很像客户端的反面。</div>
<div>
	&nbsp;</div>
<div style="text-align: center;">
	<img alt="" src="http://onecoder.qiniudn.com/8wuliao/Dpk7jvyJ/89ayf.jpg" /></div>
<div>
	服务器socket将承载从客户端接收的byte流的ChannelEvent送到pipeline中的处理器sink中。需要为pipeline配置解码器ObjectDecoder来将byte数组转换为Java对象，在本例中，Java对象是Date类型的。当Date被解码之后会被pipeline中的下一个处理器、自定义的DateHandler处理。你可能会注意到虽然Channels有一个write方 法，却没有相应的read方法。在同步API中 很多这种情况：一个线程需要等待直到数据可用才能从OutputStream（译注：这里应该是InputStream） 读取数据。这也是为什么上面的图表中没有一个指向ServerChannel箭头。同时，pipeline中最后一个处理器使用从客户端发送的所有解码 后的信息做一些有用的操作。</div>
<div>
	&nbsp;</div>
<div>
	假设服务器端的Channel pipeline是 你真实的业务服务DateReceiver的 调用发起者，pipeline为DataReceiver提供合适的解码数据。当然，你可以创建一个只包含一个处理器的pipeline，使用这个处理器完成所有的操作，但是将多个小模块链接在一起来使用更灵活。例如，如果不仅仅发送一个Date，而是一个拥有300Date的数组，我还想在客户端和服务器的pipeline中添加Compression/Decompression处理器来减少网络传输过程中的负载量，实现这个需求只需要简单的修改pipeline的 配置就行了，而不是在那个无所不能的处理器中添加新代码。或者我想增加一些认证功能防止任何客户端都能给服务器端发送Date、调整Date的时区&hellip;&hellip;模块化的方式更容易实现。</div>
<div>
	&nbsp;</div>
<div>
	服务器端的DateSender很简单。创建一个Channel工厂、pipeline工 厂，将ObjectDecorder和自定义的DateHandler放到pipeline中。 使用与ClientBootstrap相 应的ServerBootstrap， 不用类似在客户端做的连接操作，然后将ServerBootstrap绑 定到服务器端socket来监听来自于客户端的请求。</div>
<div>
	<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
public static void bootServer() {
  // More terse code to setup the server
  ServerBootstrap bootstrap = new ServerBootstrap(
    new NioServerSocketChannelFactory(
      Executors.newCachedThreadPool(),
      Executors.newCachedThreadPool()));
  // Set up the pipeline factory.
  bootstrap.setPipelineFactory(new ChannelPipelineFactory() {
   public ChannelPipeline getPipeline() throws Exception {
    return Channels.pipeline(
     new ObjectDecoder(ClassResolvers.cacheDisabled(getClass().getClassLoader())),
     new DateHandler()
    );
   };
  });
  // Bind and start to accept incoming connections.
  bootstrap.bind(new InetSocketAddress(&quot;0.0.0.0&quot;, 8080));
  slog(&quot;Listening on 8080&quot;);
 }
 static class DateHandler extends SimpleChannelHandler {
  public void messageReceived(ChannelHandlerContext ctx,MessageEvent e) throws Exception {
   Date date = (Date)e.getMessage();
   // Here&#39;s the REALLY important business service at the end of the pipeline
   slog(&quot;Hey Guys !  I got a date ! [&quot; + date + &quot;]&quot;);
   // Huh ?
   super.messageReceived(ctx, e);
  }  
 }

</pre>
</div>
<div>
	DateHandler继承了SimpleChannelHandler，这是一种好的方式：当你没有特殊的逻辑需要处理，可以使用定义好的回调。也就是说你不需要监听所有的ChannelEvents、确认是否是你需要的类型。在这种方式下，仅仅需要重 载表示接收数据的messageReceived回 调方法就可以了。</div>
<div>
	&nbsp;</div>
<div>
	slog和clog是System.out.println的 简单封装，只是输出时的前缀不同：一个是[Server]，一个是[Client]，可以用来区别不同的输出。</div>
<div>
	&nbsp;</div>
<div>
	messageReceived简单介绍</div>
<div>
	&nbsp;</div>
<div>
	之前已经说过，messageReceived是一个当接收到数据时用于回调的方法。这个方法会处理属于ChannelEvent类型的事件：MessageEvent。为了获取MessageEvent中的数据信息，只需要很简单地调用getMessage()方法就能返回一 个java.lang.Object对象，这个对象能被转换为需要的类型数据。注意：如果在这个处理器前面没有转换处理器来转换数据，那么这个数据就是ChannelBuffer类 型的。在这个例子中，ObjectDecorder已经将ChannelBuffer中的数据流转换为java.util.Data类型的日期。至于其他的对象ChannelHandlerContext， 一会就会涉及。</div>
<div>
	&nbsp;</div>
<div>
	假设业务中关键点就是记录接收 数据的日志，处理器已经从技术上完成了这个要求。那么第30行的代码（ super.messageReceived(ctx, e);） 有什么作用呢？因为在pipeline有 可能还有其他的处理器来做进一步的处理，当处理完接收的数据之后总是通过这种方式传送数据是一种很好的方案。如果没有多余的处理 器，pipeline会丢弃这个数据。</div>
<div>
	&nbsp;</div>
<div>
	如果想了解整个细节，可以去GitHub查 看DateSender的 源码，下面是代码执行的输出：</div>
<div>
	&nbsp;</div>
<blockquote>
	<div>
		[Server]:Listening on 8080</div>
	<div>
		[Client]:DateSender Example</div>
	<div>
		[Client]:Issuing Channel Connect...</div>
	<div>
		[Client]:Waiting for Channel Connect...</div>
	<div>
		[Client]:Connected. Sending Date</div>
	<div>
		[Server]:Hey Guys ! &nbsp;I got a date ! [Sat May 19 14:00:58 EDT 2012]</div>
</blockquote>
<div>
	&nbsp;</div>
<div>
	注意这个示例是单向的，只是将日期数据上传导服务器，并没有任何返回。如果 需要返回，在服务器端和客户端的pipeline的都需要相应的处理器， 在后面会讨论。</div>
<div>
	&nbsp;</div>
<div>
	<span style="color: rgb(25, 25, 25); font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px; background-color: rgb(251, 251, 251);">原文链接：&nbsp;</span><a class="external" href="http://seeallhearall.blogspot.com/2012/05/netty-tutorial-part-1-introduction-to.html" rel="nofollow" style="margin: 0px; padding: 0px; font-size: 14px; vertical-align: baseline; background-color: rgb(251, 251, 251); outline: none; text-decoration: none; border-color: rgb(8, 155, 195); color: rgb(8, 155, 195); font-family: 微软雅黑, Verdana, sans-serif, 宋体; line-height: 22px;" target="_blank">seeallhearall.blogspot</a><span style="color: rgb(25, 25, 25); font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px; background-color: rgb(251, 251, 251);">&nbsp;翻译：&nbsp;</span><a href="http://www.importnew.com/" style="margin: 0px; padding: 0px; font-size: 14px; vertical-align: baseline; background-color: rgb(251, 251, 251); outline: none; text-decoration: none; border-color: rgb(8, 155, 195); color: rgb(8, 155, 195); font-family: 微软雅黑, Verdana, sans-serif, 宋体; line-height: 22px;">ImportNew.com&nbsp;</a><span style="color: rgb(25, 25, 25); font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px; background-color: rgb(251, 251, 251);">-&nbsp;</span><a href="http://www.importnew.com/author/liuhaibo" style="margin: 0px; padding: 0px; font-size: 14px; vertical-align: baseline; background-color: rgb(251, 251, 251); outline: none; text-decoration: none; border-color: rgb(8, 155, 195); color: rgb(8, 155, 195); font-family: 微软雅黑, Verdana, sans-serif, 宋体; line-height: 22px;">刘海波</a><br style="color: rgb(25, 25, 25); font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px; background-color: rgb(251, 251, 251);" />
	<span style="color: rgb(25, 25, 25); font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px; background-color: rgb(251, 251, 251);">译文链接：&nbsp;</span><a href="http://www.importnew.com/7686.html" style="margin: 0px; padding: 0px; font-size: 14px; vertical-align: baseline; background-color: rgb(251, 251, 251); outline: none; text-decoration: none; border-color: rgb(8, 155, 195); color: rgb(8, 155, 195); font-family: 微软雅黑, Verdana, sans-serif, 宋体; line-height: 22px;">http://www.importnew.com/7686.html</a></div>
<div>
	<br style="color: rgb(25, 25, 25); font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 16px; font-weight: bold; line-height: 22px; background-color: rgb(251, 251, 251);" />
	<img src="http://www.importnew.com/importnew-weixin.jpg" style="margin: 0px; padding: 0px; border: 0px; outline: 0px; font-size: 16px; vertical-align: baseline; background-color: rgb(251, 251, 251); color: rgb(25, 25, 25); font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-weight: bold; line-height: 22px;" /></div>

