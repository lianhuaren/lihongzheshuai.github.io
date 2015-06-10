---
layout: post
title: Java NIO框架Netty教程（五）- 消息收发次数不匹配的问题
date: 2012-07-16 19:52
author: onecoder
comments: true
categories: [ChannelBuffer, Netty, 缓冲池]
---
<p>
	<span style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">本来周末是最好的学习时间，不过这周末收房子，可想而知事情自然也不会少。这段时间的周末，可能很少有时间学习了。见缝插针吧。</span></p>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	不说废话了，好好学习。<a href="http://www.coderli.com/archives/netty-channel-stream/" style="cursor: pointer; ">上回</a>通过代码理解了Netty底层信息的流的传递机制，不过只是一个感性上的认识。教会你应该如何使用和使用的时候应该注意的方面。但是有一些细节的问题，并没有提及。比如在上回（《<a href="http://www.coderli.com/archives/netty-channel-stream/" style="cursor: pointer; ">Java NIO框架Netty教程（四）- ChannelBuffer》</a>）的代码里，我们通过:</div>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
private void sendMessageByFrame(ChannelStateEvent e) {
		String msgOne = &quot;Hello, &quot;;
		String msgTwo = &quot;I&#39;m &quot;;
		String msgThree = &quot;client.&quot;;
		e.getChannel().write(tranStr2Buffer(msgOne));
		e.getChannel().write(tranStr2Buffer(msgTwo));
		e.getChannel().write(tranStr2Buffer(msgThree));
	}
</pre>
<p>
	<span style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">这样的方式，连续返送三次消息。但是如果你在服务端进行接收计数的话，你会发现，大部分时候都是接收到两次的事件请求。不过消息都是完整的。网上也有人提到过，进行10000次的连续放松，往往接受到的消息个数是999X的，总是就是消息数目上不匹配，这又是为何呢？笔者也只能通过阅读Netty的源码来找原因，我们一起来慢慢分析吧。</span></p>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	<span style="text-align: -webkit-auto; ">起点自然是选择在e.getChannel().writer()方法上。一路跟踪首先来到了：AbstractNioWorker.java类</span></div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	&nbsp;</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
protected void write0(AbstractNioChannel&lt;?&gt; channel) {
        boolean open = true;
        boolean addOpWrite = false;
        boolean removeOpWrite = false;
        boolean iothread = isIoThread(channel);

        long writtenBytes = 0;

        final SocketSendBufferPool sendBufferPool = this.sendBufferPool;
        final WritableByteChannel ch = channel.channel;
        final Queue&lt;MessageEvent&gt; writeBuffer = channel.writeBufferQueue;
        final int writeSpinCount = channel.getConfig().getWriteSpinCount();
        synchronized (channel.writeLock) {
            channel.inWriteNowLoop = true;
            for (;;) {
                MessageEvent evt = channel.currentWriteEvent;
                SendBuffer buf;
                if (evt == null) {
                    if ((channel.currentWriteEvent = evt = writeBuffer.poll()) == null) {
                        removeOpWrite = true;
                        channel.writeSuspended = false;
                        break;
                    }

                    channel.currentWriteBuffer = buf = sendBufferPool.acquire(evt.getMessage());
                } else {
                    buf = channel.currentWriteBuffer;
                }

                ChannelFuture future = evt.getFuture();
                try {
                    long localWrittenBytes = 0;
                    for (int i = writeSpinCount; i &gt; 0; i --) {
                        localWrittenBytes = buf.transferTo(ch);
                        if (localWrittenBytes != 0) {
                            writtenBytes += localWrittenBytes;
                            break;
                        }
                        if (buf.finished()) {
                            break;
                        }
                    }

                    if (buf.finished()) {
                        // Successful write - proceed to the next message.
                        buf.release();
                        channel.currentWriteEvent = null;
                        channel.currentWriteBuffer = null;
                        evt = null;
                        buf = null;
                        future.setSuccess();
                    } else {
                        // Not written fully - perhaps the kernel buffer is full.
                        addOpWrite = true;
                        channel.writeSuspended = true;

                        if (localWrittenBytes &gt; 0) {
                            // Notify progress listeners if necessary.
                            future.setProgress(
                                    localWrittenBytes,
                                    buf.writtenBytes(), buf.totalBytes());
                        }
                        break;
                    }
                } catch (AsynchronousCloseException e) {
                    // Doesn&#39;t need a user attention - ignore.
                } catch (Throwable t) {
                    if (buf != null) {
                        buf.release();
                    }
                    channel.currentWriteEvent = null;
                    channel.currentWriteBuffer = null;
                    buf = null;
                    evt = null;
                    future.setFailure(t);
                    if (iothread) {
                        fireExceptionCaught(channel, t);
                    } else {
                        fireExceptionCaughtLater(channel, t);
                    }
                    if (t instanceof IOException) {
                        open = false;
                        close(channel, succeededFuture(channel));
                    }
                }
            }
            channel.inWriteNowLoop = false;

            // Initially, the following block was executed after releasing
            // the writeLock, but there was a race condition, and it has to be
            // executed before releasing the writeLock:
            //
            //     https://issues.jboss.org/browse/NETTY-410
            //
            if (open) {
                if (addOpWrite) {
                    setOpWrite(channel);
                } else if (removeOpWrite) {
                    clearOpWrite(channel);
                }
            }
        }
        if (iothread) {
            fireWriteComplete(channel, writtenBytes);
        } else {
            fireWriteCompleteLater(channel, writtenBytes);
        }
    }
</pre>
</div>
<p>
	<span style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">这里，</span><span style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; color: rgb(255, 0, 0); ">&nbsp;</span><span style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; color: rgb(255, 0, 0); ">buf.transferTo(ch);</span><span style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">的就是调用底层WritableByteChannel的write方法，把buffer写到管道里，传递过去。通过Debug可以看到，没调用一次这个方法，服务端的messageReceived方法就会进入断点一次。当然这个也只是表相，或者说也是在预料之内的。因为笔者从开始就怀疑是连续写入过快导致的问题，所以测试过每次write后停顿1秒。再write下一次。结果一切正常。</span></p>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	&nbsp;</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	那么我们跟到这里的意义何在呢？笔者的思路是先证明不是在write端出现的写覆盖的问题，这样就可以从read端寻找问题。这里笔者也在这里加入了一个计数，测试究竟transferTo了几次。结果确实是3次。</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
for (int i = writeSpinCount; i &gt; 0; i --) {
                        localWrittenBytes = buf.transferTo(ch);
                        System.out.println(++count);</pre>
</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	接下来就从接收端找找原因，在NioWorker的read方法，实现如下：</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
&nbsp;&nbsp;&nbsp;&nbsp;@Override
    protected boolean read(SelectionKey k) {
        final SocketChannel ch = (SocketChannel) k.channel();
        final NioSocketChannel channel = (NioSocketChannel) k.attachment();

        final ReceiveBufferSizePredictor predictor =
            channel.getConfig().getReceiveBufferSizePredictor();
        final int predictedRecvBufSize = predictor.nextReceiveBufferSize();

        int ret = 0;
        int readBytes = 0;
        boolean failure = true;

        ByteBuffer bb = recvBufferPool.acquire(predictedRecvBufSize);
        try {
            while ((ret = ch.read(bb)) &gt; 0) {
                readBytes += ret;
                if (!bb.hasRemaining()) {
                    break;
                }
            }
            failure = false;
        } catch (ClosedChannelException e) {
            // Can happen, and does not need a user attention.
        } catch (Throwable t) {
            fireExceptionCaught(channel, t);
        }

        if (readBytes &gt; 0) {
            bb.flip();

            final ChannelBufferFactory bufferFactory =
                channel.getConfig().getBufferFactory();
            final ChannelBuffer buffer = bufferFactory.getBuffer(readBytes);
            buffer.setBytes(0, bb);
            buffer.writerIndex(readBytes);

            recvBufferPool.release(bb);

            // Update the predictor.
            predictor.previousReceiveBufferSize(readBytes);

            // Fire the event.
            fireMessageReceived(channel, buffer);
        } else {
            recvBufferPool.release(bb);
        }

        if (ret &lt; 0 || failure) {
            k.cancel(); // Some JDK implementations run into an infinite loop without this.
            close(channel, succeededFuture(channel));
            return false;
        }

        return true;
    }
</pre>
</div>
<p>
	<span style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">在这个方法的外层是一个循环，不停的遍历，如果有SelectionKey k存在，则进入此方法读取buffer中的数据。这个SelectionKey&nbsp;区分只是一种类型，这个设计到Java NIO中的Seletor机制，这个笔者准备下讲穿插一下。属于Netty底层的一个重要的机制。</span></p>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	<span style="text-align: -webkit-auto; ">messageReceived事件的触发，是在读取完当前缓冲池中所有的信息之后在触发的。这倒是可以解释，为什么即使我们收到事件的次数少，但是消息是完整的。</span></div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	&nbsp;</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	从目前来看，Netty通过Java 的NIO机制传递数据，数据读写跟事件没有严格的绑定机制。数据是以流的形式独立存在，读写都有一个缓冲池。</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	&nbsp;</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	不过，这些还远未解决笔者的疑惑。笔者决定先了解一下Seletor机制，再回头来探索这个问题。</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	&nbsp;</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	<span style="color:#ff0000;">待解决&hellip;&hellip;，如果您知道，热切期待您的指导。</span></div>

