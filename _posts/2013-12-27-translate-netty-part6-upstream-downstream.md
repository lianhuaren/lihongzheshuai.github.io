---
layout: post
title: 国外Netty教程翻译—Part6—Upstream、Downstream 
date: 2013-12-27 09:43
author: onecoder
comments: true
categories: [Java, Netty, Netty]
---
本文是Netty教程的第六篇。

你可能会有疑问ObjectEncoder和ObjectDecorder在pipeline的一个处理流程中为什么不会被混淆调用，毕竟它们都在同一个pipeline中，如何做到不被同时调用呢？这是因为处理器被声明Upstream类型、Downstream类型或者Upstream+Downstream类型。当pipeline发送数据给远端会调用Downstream处理器，当从远端读取数据的时候会调用Upstream处理器。如果这些专业不够直观，可以类比下面的描述：

如果需要学习东西，你需要阅读（read up）然后做笔记（write down）。无论如何，pipeline中的处理器需要直接或者间接实现下面的某个或者所有接口：
<ul>
	<li>    org.jboss.netty.channel.ChannelDownstreamHandler：由downstream调用。</li>
	<li>    org.jboss.netty.channel.ChannelUpstreamHandler: 由upstream调用。</li>
</ul>
<p style="text-align: center;">因此，在downstream写请求的时候，pipeline安排数据传送给所有实现ChannelDownstreamHandler的处理器，读请求也是相同的流程。需要注意的是，一个处理器可以同时实现这2个接口，就可以处理upstream和download事件了。</p>
<img class="alignnone" src="http://onecoder.qiniudn.com/8wuliao/DpQbgUgX/MZsdo.jpg" alt="" width="554" height="523" />
Upstream和Downstream作用于客户端和服务器端。在客户端和服务器端的一次会话中，双方都需要一对设定正确顺序的编码器和解码器。下解释了一个简单的HTTP服务器给客户端请求提供文件的流程：
<p style="text-align: center;"><img class="alignnone" src="http://onecoder.qiniudn.com/8wuliao/DpQbh3q1/KuPIm.jpg" alt="" width="554" height="255" /></p>
pipleline中ChannelHandlers 的执行顺序

为了实现有序的修改数据，pipeline严格保持处理器的顺序。例如一个pipeline类似这样：

1、JSON 编码器
<ul>
	<li>    接收: 对象部件</li>
	<li>    生成: 根据对象生成的JSON字符串</li>
</ul>
2、String 编码器
<ul>
	<li>    接收: 字符串</li>
	<li>    生成: 容纳编码后的字符串的ChannelBuffer</li>
</ul>
如果这些编码的顺序偶然被调转，String编码器就会接收到一个对象部件。String编码器认为这是一个字符串，然后就会抛出类型转换异常。有一些构造器负责执行pipeline中的处理顺序。最简单的是org.jboss.netty.channel.Channels类中的pipeline静态方法，它创建了一个用于存储handlers的pipeline，pipeline中handler的顺序以数组中的顺序为准。构造器的声明如下：
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">static ChannelPipeline Channels.pipeline(ChannelHandler...handlers)
</pre>
在pipeline的结构允许指定处理器的顺序。最简单的是org.jboss.netty.channel.Channels类中的静态方法 pipeline，会创建一个pipeline然后根据传入的处理器数组元素的顺序将处理器存放到pipeline中。方法：
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">static ChannelPipeline Channels.pipeline(ChannelHandler...handlers)
</pre>
在ChannelPipeline接口中提供了可以将处理器添加到指定位置的方法，因此我们可以给pipeline中的处理器指定特定的逻辑名称来标识每个处理器的唯一性。名称可以是任意的字符串，但是有时能够根据名称来访问处理器，或者通过名称来确定处理器的位置。处理器的名称是必须存在的，如果没有人为设定，会被自动指定一个名称。pipeline中的方法如下：
<ul>
	<li>    addAfter(String baseName, String name, ChannelHandler handler)：在pipeline中添加一个处理器放置到名为baseName的处理器后面，并且将新添加的处理器命名为name。</li>
	<li>    addBefore(String baseName, String name, ChannelHandler handler)：在pipeline中添加一个处理器放置到名为baseName的处理器前面，并且将新添加的处理器命名为name。</li>
	<li>    addFirst(String name, ChannelHandler handler)：在pipeline中添加一个处理器放置到pipeline第一个位置，并且将新添加的处理器命名为name。</li>
	<li>    addLast(String name, ChannelHandler handler)：在pipeline中添加一个处理器放置到pipeline最后一个位置， 并且将新添加的处理器命名为name。</li>
</ul>
动态修改pipeline中的ChannelHandler

pipeline另一个特性是可以被修改的，所以在运行过程中可以添加或者删除pipeline的处理器。为了实现这个特性，Netty的pipeline是线程安全的。上面的4个方法可以在运行时被调用来调整pipeline适应某种特定的执行流程或转换状态。

实际上有个不可被修改的pipeline实现：StaticChannelPipeline。如果这个对象被创建后就不可以被修改，因为pipeline在运行过程中不需要考虑会被修改，可以提供更好的性能。

在创建pipeline的过程中已经完成了这些繁琐的步骤，为什么还需要修改pipeline中的处理器呢？这里有个假设示例：在客户端的pipeline中有一串的处理器，最后一个处理器是ZlibEncoder，用于在数据发送之前压缩数据。如果数据量少于1024byte，压缩操作会影响性能：降低性能。这意味着需要有条件的使用压缩处理器处理数据。在将数据传入到处理器之前是不知道数据的大小的，不确定是创建一个支持压缩的pipeline或者一个不支持压缩的pipeline。为了解决这个问题，可以创建一个内容审计处理器用来检测数据的大小来决定是否需要压缩处理器。将这个审计处理放置在压缩处理器之前、其他处理器之后就可以了。

现在，你可以得出有多种方案，这个场景是假设的，请听我继续。

首先，创建一个支持压缩特性的pipeline，然后注意ConditionalCompressionHandler，现在 pipeline是这样的：
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">public ChannelPipeline getPipeline() throws Exception {
 ChannelPipeline pipeline = Channels.pipeline();
 /// Add all the pre-amble handlers
 //pipeline.addLast("Foo", new SomeHandler());
 //pipeline.addLast("Bar", new SomeOtherHandler());
 // etc.
 pipeline.addLast("IAmTheDecider", new ConditionalCompressionHandler(1024, "MyCompressionHandler"));
 pipeline.addLast("MyCompressionHandler", new ZlibEncoder());
 return pipeline;
}
</pre>
ConditionalCompressionHandler一定要检查传输的ChannelBuffer的大小，然后决定压缩处理器是否会被调用。然而如果压缩处理器被移除，下次传入的数据需要被压缩，那么就要将压缩处理器重新添加到pipeline中。
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">public class ConditionalCompressionHandler extends SimpleChannelDownstreamHandler {
 /** The minimum size of a payload to be compressed */
 protected final int sizeThreshold;
 /** The name of the handler to remove if the payload is smaller than specified sizeThreshold */
 protected final String nameOfCompressionHandler;
 /** The compression handler */
 protected volatile ChannelHandler compressionHandler = null;
 
 /**
  * Creates a new ConditionalCompressionHandler
  * @param sizeThreshold The minimum size of a payload to be compressed
  * @param nameOfCompressionHandler The name of the handler to remove if the payload is smaller than specified sizeThreshold
  */
 public ConditionalCompressionHandler(int sizeThreshold, String nameOfCompressionHandler) {
  this.sizeThreshold = sizeThreshold;
  this.nameOfCompressionHandler = nameOfCompressionHandler;
 }
 
 /**
  * see org.jboss.netty.channel.SimpleChannelDownstreamHandler
  */
 public void writeRequested(ChannelHandlerContext ctx, MessageEvent e) {
  // If the message is not a ChannelBuffer, hello ClassCastException !
  ChannelBuffer cb = (ChannelBuffer)e.getMessage();
  // Check to see if we already removed the handler
  boolean pipelineContainsCompressor = ctx.getPipeline().getContext(nameOfCompressionHandler)!=null;
  if(cb.readableBytes() &lt; sizeThreshold) {
   if(pipelineContainsCompressor) {
    // The payload is too small to be compressed but the pipeline contains the compression handler
    // so we need to remove it.
    compressionHandler = ctx.getPipeline().remove(nameOfCompressionHandler);
   }
  } else {
   // We want to compress the payload, let's make sure the compressor is there
   if(!pipelineContainsCompressor) {
    // Oops, it's not there, so lets put it in
    ctx.getPipeline().addAfter(ctx.getName(), nameOfCompressionHandler , compressionHandler);
   }
  }
 }
 
}

</pre>
ChannelHandlerContext

在响应所有的处理器事件的时候，会将ChannelHandlerContext传递给处理器。上述的示例中主要展示ChannelHandlerContext是一个用于ChannelHandler和容纳此Handler的ChannelPipeline交互的适配器。而且，还允许一个处理器查询其他处理器，并且间接和其他处理器进行交互。
<ul>
	<li>    在26行，确认压缩处理器是否在ChannelHandlerContext的pipeline中是否存在，然后从 ChannelHandlerContext中获取压缩处理器.如果处理器不存在，当前调用返回null。</li>
	<li>    在31行，访问pipeline根据处理器的名称删除压缩处理器。</li>
	<li>    在37行，访问pipeline将压缩处理器添加到pipeline中。通过ChannelHandlerContext的getName()方法获取当前处理器的名称</li>
</ul>
原文链接： <a class="external" href="http://seeallhearall.blogspot.com/2012/05/netty-tutorial-part-1-introduction-to.html" target="_blank" rel="nofollow">seeallhearall.blogspot</a> 翻译： <a href="http://www.importnew.com"> ImportNew.com </a> - <a href="http://www.importnew.com/author/liuhaibo">刘海波</a>
译文链接： <a href="http://www.importnew.com/7699.html">http://www.importnew.com/7699.html</a>
