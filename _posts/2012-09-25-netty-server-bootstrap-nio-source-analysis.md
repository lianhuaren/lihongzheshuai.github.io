---
layout: post
title: Java NIO框架Netty教程(十六)-ServerBootStrap启动流程源码分析
date: 2012-09-25 13:42
author: onecoder
comments: true
categories: [Netty, Netty, serverbootstrap, 源码]
---
<p>
	有一段事件没有更新文章了，各种原因都有吧。搬家的琐事，搬家后的安逸呵呵。不过，<a href="http://www.coderli.com">OneCoder</a>明白，绝不能放松。对于Netty的学习，也该稍微深入一点了。</p>
<div>
	所以，这次<a href="http://www.coderli.com">OneCoder</a>花了几天时间，仔细梳理了一下Netty的源码，总结了一下ServerBootStrap的启动和任务处理流程，基本涵盖了Netty的关键架构。</div>
<div>
	&nbsp;</div>
<div>
	<a href="http://www.coderli.com">OneCoder</a>总结了一张流程图：</div>
<div>
	&nbsp;</div>
<div style="text-align: center; ">
	<img alt="netty-bootstrap" src="http://onecoder.qiniudn.com/8wuliao/Ci6qURH0/aGtVD.jpg" style="width: 640px; height: 588px;" /></div>
<div>
	&nbsp;</div>
<div>
	<div>
		该图是<a href="http://www.coderli.com">OneCoder</a>通过阅读Netty源码，逐渐记录下来的。基本可以说明Netty服务的启动流程。这里在具体讲解一下。</div>
	<div>
		&nbsp;</div>
	<div>
		首先说明，我们这次顺利的流程是基于NioSocketServer的。也就是基于Java NIO Selector的实现方式。在第六讲<a href="http://www.coderli.com/netty-nio-selector">《Java NIO框架Netty教程(六)-Java NIO Selector模式》</a>中，我们已经知道使用Selector的几个关键点，所以这里我们也重点关注一下，这些点在Netty中是如何使用的。</div>
	<div>
		&nbsp;</div>
	<div>
		很多看过Netty源码的人都说Netty源码写的很漂亮。可漂亮在哪呢？Netty通过一个ChannelFactory决定了你当前使用的协议类型(Nio/oio等)，比如，OneCoder这里使用的是NioServerSocket，那么需要声明的Factory即为NioServerSocketChannelFactory，声明了这个Factory，就决定了你使用的Channel，pipeline以及pipeline中，具体处理业务的sink的类型。这种使用方式十分简洁的，学习曲线很低，切换实现十分容易。</div>
	<div>
		&nbsp;</div>
	<div>
		Netty采用的是基于事件的管道式架构设计，事件(Event)在管道(Pipeline)中流转，交由(通过pipelinesink)相应的处理器(Handler)。这些关键元素类型的匹配都是由开始声明的ChannelFactory决定的。</div>
	<div>
		&nbsp;</div>
	<div>
		Netty框架内部的业务也遵循这个流程，Server端绑定端口的binder其实也是一个Handler，在构造完Binder后，便要声明一个Pipeline管道，并赋给新建一个Channel。Netty在newChannel的过程中，相应调用了Java中的ServerSocketChannel.open方法，打开一个channel。然后触发fireChannelOpen事件。这个事件的接受是可以复写的。Binder自身接收了这个事件。在事件的处理中，继续向下完成具体的端口的绑定。对应Selector中的&nbsp;socketChannel.socket().bind()。然后触发fireChannelBound事件。默认情况下，该事件无人接受，继续向下开始构造Boss线程池。我们知道在Netty中Boss线程池是用来接受和分发请求的核心线程池。所以在channel绑定后，必然要启动Boss线城池，随时准备接受client发来的请求。在Boss构造函数中，第一次注册了selector感兴趣的事件类型,SelectionKey.OP_ACCEPT。至此，在第六讲中介绍的使用Selector的几个关键步骤都体现在Netty中了。在Boss中回启动一个死循环来查询是否有感兴趣的事件发生，对于第一次的客户端的注册事件，Boss会将Channel注册给worker保存。</div>
	<div>
		&nbsp;</div>
	<div>
		这里补充一下，也是图中忽略的部分，就是关于worker线程池的初始化时机问题。worker池的构造，在最开始构造ChannelFactory的时候就已经准备好了。在NioServerSocketChannelFactory的构造函数里，会new一个NioWorkerPool。在NioWorkerPool的基类AbstractNioWorkerPool的构造函数中，会调用OpenSelector方法，其中也打开了一个selector，并且启动了worker线程池。</div>
	<div>
		<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
private void openSelector() {
        try {
            selector = Selector.open();
        } catch (Throwable t) {
            throw new ChannelException(&quot;Failed to create a selector.&quot;, t);
        }

        // Start the worker thread with the new Selector.
        boolean success = false;
        try {
            DeadLockProofWorker.start(executor, new ThreadRenamingRunnable(this, &quot;New I/O  worker #&quot; + id));
            success = true;
        } finally {
            if (!success) {
                // Release the Selector if the execution fails.
                try {
                    selector.close();
                } catch (Throwable t) {
                    logger.warn(&quot;Failed to close a selector.&quot;, t);
                }
                selector = null;
                // The method will return to the caller at this point.
            }
        }
        assert selector != null &amp;&amp; selector.isOpen();
    }
</pre>
	</div>
</div>
<p>
	至此，会分线程启动AbstractNioWorker中run逻辑。同样是循环处理任务队列。</p>
<div>
	<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
processRegisterTaskQueue();
processEventQueue();
processWriteTaskQueue();
processSelectedKeys(selector.selectedKeys());
</pre>
</div>
<div>
	<div>
		&nbsp;</div>
</div>
<div>
	这样，设计使事件的接收和处理模块完全解耦。</div>
<div>
	由此可见，如果你想从nio切换到oio，只需要构造不同的ChannelFacotry即可。果然简洁优雅。</div>

