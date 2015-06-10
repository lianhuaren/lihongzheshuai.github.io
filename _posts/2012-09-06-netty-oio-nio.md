---
layout: post
title: Java NIO框架Netty教程(十四)-Netty中OIO模型(对比NIO)
date: 2012-09-06 22:46
author: onecoder
comments: true
categories: [Netty, Netty, oio]
---
<a href="http://www.coderli.com">OneCoder</a>这个周末搬家，并且新家目前还没有网络，本周的翻译的任务尚未完成，下周一起补上，先上一篇OIO和NIO对比的小研究。

Netty中不光支持了Java中NIO模型，同时也提供了对OIO模型的支持。（New IO vs Old IO）。

首先，在Netty中，切换OIO和NIO两种模式是非常方便的，只需要初始化不同的Channel工程即可。
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">ServerBootstrap bootstrap = new ServerBootstrap(
				new OioServerSocketChannelFactory(
						Executors.newCachedThreadPool(),
						Executors.newFixedThreadPool(4)));</pre>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">ServerBootstrap bootstrap = new ServerBootstrap(
				new NioServerSocketChannelFactory(
						Executors.newCachedThreadPool(),
						Executors.newFixedThreadPool(4)));</pre>
这就是Netty框架为我们做的贡献。

再说说，这两种情况的区别。OneCoder根据网上的资料和自己的理解，总结了一下：在Netty中，是通过worker执行任务的。也就是我们在构造bootstrap时传入的worker线程池。对于传统OIO来说，一个worker对应的channel，从读到操作到再到回写，只要是这个channel的操作都通过这个worker来完成，对于NIO来说，到MessageRecieved之后，该worker的任务就完成了。所以，从这个角度来说，非常建议你在Recieve之后，立即启动线程去执行耗时逻辑，以释放worker。

基于这个分析，你可能也发现了，上面的代码中我们用的是FiexedThreadPool。固定大小为4，从理论上来说，OIO支持的client数应该是4。而NIO应该不受此影响。测试效果如下图：

8个Client连接：

<img class="aligncenter" src="http://onecoder.qiniudn.com/8wuliao/CfmGyFg7/EL2ZO.jpg" alt="" width="656" height="199" />

&nbsp;

NIOServer的线程情况：

<img src="http://onecoder.qiniudn.com/8wuliao/CfmGycm3/3WIAC.jpg" alt="" />

并且8个Client的请求都正常处理了。

对于OIO来说，如果你对worker池没有控制，那么支持8个client需要8个worker，8个线程，这也就是传统OIO并发数受限的原因，如图：

<img style="height: 221px; width: 640px;" src="http://onecoder.qiniudn.com/8wuliao/CfmGyOBT/lLEys.jpg" alt="" />

当OIO使用FixedThreadPool的时候：

<img style="width: 640px; height: 163px;" src="http://onecoder.qiniudn.com/8wuliao/CfmGyUPF/fdIHa.jpg" alt="" />

只能处理头四个client的请求，他的被堵塞了。
<blockquote>Hello action.: 32

Hello action.: 33

Hello action.: 34

Hello action.: 35

Hello action.: 36

Hello action.: 37

Hello action.: 38

Hello action.: 39

Hello action.: 40</blockquote>
但是，在Netty框架中，不论是OIO和NIO模型，读写端都不会堵塞。客户端写后立即返回，不管服务端是否接收到，接收后是否处理完成。下一章，我们将会从代码的角度来研究一下Netty中对OIO和NIO这两种模式下worker的处理方式。
