---
layout: post
title: Java NIO框架Netty教程(七)-再谈收发信息次数问题
date: 2012-07-22 13:42
author: onecoder
comments: true
categories: [Netty, Netty, selector, 缓冲池]
---
<p>
	在<a href="http://www.coderli.com/archives/netty-message-receive-count-mismatch/">《Java NIO框架Netty教程（五）- 消息收发次数不匹配的问题》</a>里我们试图分析一个消息收发次数不匹配的问题。当时笔者还是心存疑惑的。所以决定先学习一下Java NIO的<a href="http://www.coderli.com/archives/netty-nio-selector/">Selector</a>机制。</p>
<p>
	经过简单的了解，笔者大胆的猜测和&ldquo;武断&rdquo;一下该问题的原因。</p>
<p>
	首先，Selector机制让我们注册一个感兴趣的事件，然后只要有该事件发生，就会传递给接收端。我们写了三次，接收端一定会出发三次的。<br />
	然后，Netty实现机制里，有个Buffer缓冲池，把收到的信息都缓存在里面，通过一个线程统一处理。也就是我们看到的那个buffer的处理过程。<br />
	Netty的设置中，有一个一次性最多读取字节大小的设定。并且，事件的触发是在处理过缓冲池中的消息之后。我们再来回顾一下Netty中读取信息的那段代码：</p>
<p>
	&nbsp;</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
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
</pre>
<p>
	可以看到，如果没有读取到字节是不会触发事件的，所以我们可能会收到2次或者3次信息。（如果发的快，解析的慢，后两次信息，一次性读取了，就2次，如果发送间隔长，分次解析，就收到3次。）原因应该就是如此。跟我们开始猜的差不多，只是不敢确认。</p>

