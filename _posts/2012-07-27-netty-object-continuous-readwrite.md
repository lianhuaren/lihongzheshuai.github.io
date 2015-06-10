---
layout: post
title: Java NIO框架Netty教程(十)-Object对象的连续收发解析分析
date: 2012-07-27 12:52
author: onecoder
comments: true
categories: [codec, Netty, Netty, 对象]
---
如果您一直关注OneCoder，我们之前有两篇文章介绍关于Netty消息连续收发的问题。( <a href="http://www.coderli.com/archives/netty-message-receive-count-mismatch/">《Java NIO框架Netty教程（五）- 消息收发次数不匹配的问题 》</a>、<a href="http://www.coderli.com/archives/netty-message-receive-count-mismatch-two/">《 Java NIO框架Netty教程(七)-再谈收发信息次数问题 》</a>)。如果您经常的&ldquo;怀疑&rdquo;和思考，我们刚介绍过了Object的传递，您是否好奇，在Object传递中是否会有这样的问题？如果Object流的字节截断错乱，那肯定是会出错的。Netty一定不会这么傻的，那么Netty是怎么做的呢？<br />
<br />
我们先通过代码验证一下是否有这样的问题。（有问题的可能性几乎没有。）<br />
<br />
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
/**
	 * 当绑定到服务端的时候触发，给服务端发消息。
	 * 
	 * @author lihzh
	 * @alia OneCoder
	 */
	@Override
	public void channelConnected(ChannelHandlerContext ctx, ChannelStateEvent e) {
		// 向服务端发送Object信息
		sendObject(e.getChannel());
	}

	/**
	 * 发送Object
	 * 
	 * @param channel
	 * @author lihzh
	 * @alia OneCoder
	 */
	private void sendObject(Channel channel) {
		Command command = new Command();
		command.setActionName(&quot;Hello action.&quot;);
		Command commandOne = new Command();
		commandOne.setActionName(&quot;Hello action. One&quot;);
		Command command2 = new Command();
		command2.setActionName(&quot;Hello action. Two&quot;);
		channel.write(command2);
		channel.write(command);
		channel.write(commandOne);
	}
</pre>
<span style="font-family: Tahoma; font-size: 14px; ">打印结果：</span>
<div style="font-family: Tahoma; font-size: 14px; ">
	<blockquote>
		<div>
			Hello action. Two</div>
		<div>
			Hello action.</div>
		<div>
			Hello action. One</div>
	</blockquote>
</div>
<br />
一切正常。那么Netty是怎么分割对象流的呢？看看ObjectDecoder怎么做的。<br />
在ObjectDecoder的基类LengthFieldBasedFrameDecoder中注释中有详细的说明。我们这里主要介绍一下关键的代码逻辑：<br />
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
@Override
    protected Object decode(
            ChannelHandlerContext ctx, Channel channel, ChannelBuffer buffer) throws Exception {

        if (discardingTooLongFrame) {
            long bytesToDiscard = this.bytesToDiscard;
            int localBytesToDiscard = (int) Math.min(bytesToDiscard, buffer.readableBytes());
            buffer.skipBytes(localBytesToDiscard);
            bytesToDiscard -= localBytesToDiscard;
            this.bytesToDiscard = bytesToDiscard;
            failIfNecessary(ctx, false);
            return null;
        }

        if (buffer.readableBytes() &lt; lengthFieldEndOffset) {
            return null;
        }

        int actualLengthFieldOffset = buffer.readerIndex() + lengthFieldOffset;
        long frameLength;
        switch (lengthFieldLength) {
        case 1:
            frameLength = buffer.getUnsignedByte(actualLengthFieldOffset);
            break;
        case 2:
            frameLength = buffer.getUnsignedShort(actualLengthFieldOffset);
            break;
        case 3:
            frameLength = buffer.getUnsignedMedium(actualLengthFieldOffset);
            break;
        case 4:
            frameLength = buffer.getUnsignedInt(actualLengthFieldOffset);
            break;
&hellip;&hellip;
</pre>
我们这里进入的是4，还记得在编码时候的开头的4位占位字节吗？跟踪进去发现。<br />
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
public int getInt(int index) {
        return  (array[index]     &amp; 0xff) &lt;&lt; 24 |
                (array[index + 1] &amp; 0xff) &lt;&lt; 16 |
                (array[index + 2] &amp; 0xff) &lt;&lt;  8 |
                (array[index + 3] &amp; 0xff) &lt;&lt;  0;
    }
</pre>
原来，当初在编码时，在流开头增加的4字节的字符是做这个的。他记录了当前了这个对象流的长度，便于在解码时候准确的计算出该对象流的长度，正确解码。看来，我们如果我们自己写的对象编码解码的工具，要考虑的还有很多啊。<br />
<br />
附：LengthFieldBasedFrameDecoder的JavaDoc
<blockquote>
	<p>
		/**<br />
		* A decoder that splits the received {@link ChannelBuffer}s dynamically by the<br />
		* value of the length field in the message.&nbsp; It is particularly useful when you<br />
		* decode a binary message which has an integer header field that represents the<br />
		* length of the message body or the whole message.<br />
		* &lt;p&gt;<br />
		* {@link LengthFieldBasedFrameDecoder} has many configuration parameters so<br />
		* that it can decode any message with a length field, which is often seen in<br />
		* proprietary client-server protocols. Here are some example that will give<br />
		* you the basic idea on which option does what.<br />
		*<br />
		* &lt;h3&gt;2 bytes length field at offset 0, do not strip header&lt;/h3&gt;<br />
		*<br />
		* The value of the length field in this example is &lt;tt&gt;12 (0x0C)&lt;/tt&gt; which<br />
		* represents the length of &quot;HELLO, WORLD&quot;.&nbsp; By default, the decoder assumes<br />
		* that the length field represents the number of the bytes that follows the<br />
		* length field.&nbsp; Therefore, it can be decoded with the simplistic parameter<br />
		* combination.<br />
		* &lt;pre&gt;<br />
		* &lt;b&gt;lengthFieldOffset&lt;/b&gt;&nbsp;&nbsp; = &lt;b&gt;0&lt;/b&gt;<br />
		* &lt;b&gt;lengthFieldLength&lt;/b&gt;&nbsp;&nbsp; = &lt;b&gt;2&lt;/b&gt;<br />
		* lengthAdjustment&nbsp;&nbsp;&nbsp; = 0<br />
		* initialBytesToStrip = 0 (= do not strip header)<br />
		*<br />
		* BEFORE DECODE (14 bytes)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; AFTER DECODE (14 bytes)<br />
		* +--------+----------------+&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; +--------+----------------+<br />
		* | Length | Actual Content |-----&gt;| Length | Actual Content |<br />
		* | 0x000C | &quot;HELLO, WORLD&quot; |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 0x000C | &quot;HELLO, WORLD&quot; |<br />
		* +--------+----------------+&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; +--------+----------------+<br />
		* &lt;/pre&gt;<br />
		*<br />
		* &lt;h3&gt;2 bytes length field at offset 0, strip header&lt;/h3&gt;<br />
		*<br />
		* Because we can get the length of the content by calling<br />
		* {@link ChannelBuffer#readableBytes()}, you might want to strip the length<br />
		* field by specifying &lt;tt&gt;initialBytesToStrip&lt;/tt&gt;.&nbsp; In this example, we<br />
		* specified &lt;tt&gt;2&lt;/tt&gt;, that is same with the length of the length field, to<br />
		* strip the first two bytes.<br />
		* &lt;pre&gt;<br />
		* lengthFieldOffset&nbsp;&nbsp; = 0<br />
		* lengthFieldLength&nbsp;&nbsp; = 2<br />
		* lengthAdjustment&nbsp;&nbsp;&nbsp; = 0<br />
		* &lt;b&gt;initialBytesToStrip&lt;/b&gt; = &lt;b&gt;2&lt;/b&gt; (= the length of the Length field)<br />
		*<br />
		* BEFORE DECODE (14 bytes)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; AFTER DECODE (12 bytes)<br />
		* +--------+----------------+&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; +----------------+<br />
		* | Length | Actual Content |-----&gt;| Actual Content |<br />
		* | 0x000C | &quot;HELLO, WORLD&quot; |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | &quot;HELLO, WORLD&quot; |<br />
		* +--------+----------------+&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; +----------------+<br />
		* &lt;/pre&gt;<br />
		*<br />
		* &lt;h3&gt;2 bytes length field at offset 0, do not strip header, the length field<br />
		*&nbsp;&nbsp;&nbsp;&nbsp; represents the length of the whole message&lt;/h3&gt;<br />
		*<br />
		* In most cases, the length field represents the length of the message body<br />
		* only, as shown in the previous examples.&nbsp; However, in some protocols, the<br />
		* length field represents the length of the whole message, including the<br />
		* message header.&nbsp; In such a case, we specify a non-zero<br />
		* &lt;tt&gt;lengthAdjustment&lt;/tt&gt;.&nbsp; Because the length value in this example message<br />
		* is always greater than the body length by &lt;tt&gt;2&lt;/tt&gt;, we specify &lt;tt&gt;-2&lt;/tt&gt;<br />
		* as &lt;tt&gt;lengthAdjustment&lt;/tt&gt; for compensation.<br />
		* &lt;pre&gt;<br />
		* lengthFieldOffset&nbsp;&nbsp; =&nbsp; 0<br />
		* lengthFieldLength&nbsp;&nbsp; =&nbsp; 2<br />
		* &lt;b&gt;lengthAdjustment&lt;/b&gt;&nbsp;&nbsp;&nbsp; = &lt;b&gt;-2&lt;/b&gt; (= the length of the Length field)<br />
		* initialBytesToStrip =&nbsp; 0<br />
		*<br />
		* BEFORE DECODE (14 bytes)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; AFTER DECODE (14 bytes)<br />
		* +--------+----------------+&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; +--------+----------------+<br />
		* | Length | Actual Content |-----&gt;| Length | Actual Content |<br />
		* | 0x000E | &quot;HELLO, WORLD&quot; |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 0x000E | &quot;HELLO, WORLD&quot; |<br />
		* +--------+----------------+&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; +--------+----------------+<br />
		* &lt;/pre&gt;<br />
		*<br />
		* &lt;h3&gt;3 bytes length field at the end of 5 bytes header, do not strip header&lt;/h3&gt;<br />
		*<br />
		* The following message is a simple variation of the first example.&nbsp; An extra<br />
		* header value is prepended to the message.&nbsp; &lt;tt&gt;lengthAdjustment&lt;/tt&gt; is zero<br />
		* again because the decoder always takes the length of the prepended data into<br />
		* account during frame length calculation.<br />
		* &lt;pre&gt;<br />
		* &lt;b&gt;lengthFieldOffset&lt;/b&gt;&nbsp;&nbsp; = &lt;b&gt;2&lt;/b&gt; (= the length of Header 1)<br />
		* &lt;b&gt;lengthFieldLength&lt;/b&gt;&nbsp;&nbsp; = &lt;b&gt;3&lt;/b&gt;<br />
		* lengthAdjustment&nbsp;&nbsp;&nbsp; = 0<br />
		* initialBytesToStrip = 0<br />
		*<br />
		* BEFORE DECODE (17 bytes)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; AFTER DECODE (17 bytes)<br />
		* +----------+----------+----------------+&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; +----------+----------+----------------+<br />
		* | Header 1 |&nbsp; Length&nbsp; | Actual Content |-----&gt;| Header 1 |&nbsp; Length&nbsp; | Actual Content |<br />
		* |&nbsp; 0xCAFE&nbsp; | 0x00000C | &quot;HELLO, WORLD&quot; |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |&nbsp; 0xCAFE&nbsp; | 0x00000C | &quot;HELLO, WORLD&quot; |<br />
		* +----------+----------+----------------+&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; +----------+----------+----------------+<br />
		* &lt;/pre&gt;<br />
		*<br />
		* &lt;h3&gt;3 bytes length field at the beginning of 5 bytes header, do not strip header&lt;/h3&gt;<br />
		*<br />
		* This is an advanced example that shows the case where there is an extra<br />
		* header between the length field and the message body.&nbsp; You have to specify a<br />
		* positive &lt;tt&gt;lengthAdjustment&lt;/tt&gt; so that the decoder counts the extra<br />
		* header into the frame length calculation.<br />
		* &lt;pre&gt;<br />
		* lengthFieldOffset&nbsp;&nbsp; = 0<br />
		* lengthFieldLength&nbsp;&nbsp; = 3<br />
		* &lt;b&gt;lengthAdjustment&lt;/b&gt;&nbsp;&nbsp;&nbsp; = &lt;b&gt;2&lt;/b&gt; (= the length of Header 1)<br />
		* initialBytesToStrip = 0<br />
		*<br />
		* BEFORE DECODE (17 bytes)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; AFTER DECODE (17 bytes)<br />
		* +----------+----------+----------------+&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; +----------+----------+----------------+<br />
		* |&nbsp; Length&nbsp; | Header 1 | Actual Content |-----&gt;|&nbsp; Length&nbsp; | Header 1 | Actual Content |<br />
		* | 0x00000C |&nbsp; 0xCAFE&nbsp; | &quot;HELLO, WORLD&quot; |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 0x00000C |&nbsp; 0xCAFE&nbsp; | &quot;HELLO, WORLD&quot; |<br />
		* +----------+----------+----------------+&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; +----------+----------+----------------+<br />
		* &lt;/pre&gt;<br />
		*<br />
		* &lt;h3&gt;2 bytes length field at offset 1 in the middle of 4 bytes header,<br />
		*&nbsp;&nbsp;&nbsp;&nbsp; strip the first header field and the length field&lt;/h3&gt;<br />
		*<br />
		* This is a combination of all the examples above.&nbsp; There are the prepended<br />
		* header before the length field and the extra header after the length field.<br />
		* The prepended header affects the &lt;tt&gt;lengthFieldOffset&lt;/tt&gt; and the extra<br />
		* header affects the &lt;tt&gt;lengthAdjustment&lt;/tt&gt;.&nbsp; We also specified a non-zero<br />
		* &lt;tt&gt;initialBytesToStrip&lt;/tt&gt; to strip the length field and the prepended<br />
		* header from the frame.&nbsp; If you don&#39;t want to strip the prepended header, you<br />
		* could specify &lt;tt&gt;0&lt;/tt&gt; for &lt;tt&gt;initialBytesToSkip&lt;/tt&gt;.<br />
		* &lt;pre&gt;<br />
		* lengthFieldOffset&nbsp;&nbsp; = 1 (= the length of HDR1)<br />
		* lengthFieldLength&nbsp;&nbsp; = 2<br />
		* &lt;b&gt;lengthAdjustment&lt;/b&gt;&nbsp;&nbsp;&nbsp; = &lt;b&gt;1&lt;/b&gt; (= the length of HDR2)<br />
		* &lt;b&gt;initialBytesToStrip&lt;/b&gt; = &lt;b&gt;3&lt;/b&gt; (= the length of HDR1 + LEN)<br />
		*<br />
		* BEFORE DECODE (16 bytes)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; AFTER DECODE (13 bytes)<br />
		* +------+--------+------+----------------+&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; +------+----------------+<br />
		* | HDR1 | Length | HDR2 | Actual Content |-----&gt;| HDR2 | Actual Content |<br />
		* | 0xCA | 0x000C | 0xFE | &quot;HELLO, WORLD&quot; |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 0xFE | &quot;HELLO, WORLD&quot; |<br />
		* +------+--------+------+----------------+&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; +------+----------------+<br />
		* &lt;/pre&gt;<br />
		*<br />
		* &lt;h3&gt;2 bytes length field at offset 1 in the middle of 4 bytes header,<br />
		*&nbsp;&nbsp;&nbsp;&nbsp; strip the first header field and the length field, the length field<br />
		*&nbsp;&nbsp;&nbsp;&nbsp; represents the length of the whole message&lt;/h3&gt;<br />
		*<br />
		* Let&#39;s give another twist to the previous example.&nbsp; The only difference from<br />
		* the previous example is that the length field represents the length of the<br />
		* whole message instead of the message body, just like the third example.<br />
		* We have to count the length of HDR1 and Length into &lt;tt&gt;lengthAdjustment&lt;/tt&gt;.<br />
		* Please note that we don&#39;t need to take the length of HDR2 into account<br />
		* because the length field already includes the whole header length.<br />
		* &lt;pre&gt;<br />
		* lengthFieldOffset&nbsp;&nbsp; =&nbsp; 1<br />
		* lengthFieldLength&nbsp;&nbsp; =&nbsp; 2<br />
		* &lt;b&gt;lengthAdjustment&lt;/b&gt;&nbsp;&nbsp;&nbsp; = &lt;b&gt;-3&lt;/b&gt; (= the length of HDR1 + LEN, negative)<br />
		* &lt;b&gt;initialBytesToStrip&lt;/b&gt; = &lt;b&gt; 3&lt;/b&gt;<br />
		*<br />
		* BEFORE DECODE (16 bytes)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; AFTER DECODE (13 bytes)<br />
		* +------+--------+------+----------------+&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; +------+----------------+<br />
		* | HDR1 | Length | HDR2 | Actual Content |-----&gt;| HDR2 | Actual Content |<br />
		* | 0xCA | 0x0010 | 0xFE | &quot;HELLO, WORLD&quot; |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 0xFE | &quot;HELLO, WORLD&quot; |<br />
		* +------+--------+------+----------------+&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; +------+----------------+<br />
		* &lt;/pre&gt;<br />
		*<br />
		* @see LengthFieldPrepender<br />
		*/</p>
</blockquote>

