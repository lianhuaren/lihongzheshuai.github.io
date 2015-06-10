---
layout: post
title: Restlet 客户端连接超时问题解决
date: 2013-12-16 13:05
author: onecoder
comments: true
categories: [connection, restlet, restlet, timeout]
---
<p>
	使用Restlet进行同步请求，有时可能处理的时间会很长所以需要客户端进行较长时间的等待。从API中查得客户端的设置方式如下：<br />
	&nbsp;</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
ClientResource client = new ClientResource(new Context(), uri);
client.setRetryAttempts(0);
client.setProtocol(protocol);
client.getContext().getParameters().add(&quot;socketTimeout&quot;, &quot;60000&quot;);
</pre>
<p>
	不过，设置后，OneCoder经测试却发现无效。无论socketTimeout设置为多少。均会在1分钟左右超时。<br />
	<br />
	这好像是由于Restlet默认使用的是一个简单的http服务，而在现在版本中，Restlet提供了很多增强的扩展。其中一个是ext.jetty扩展。即以jetty服务器做服务，启动Rest服务。使用方式很简单，无需修改原有代码，只需增加ext.jetty的依赖即可。</p>
<pre class="brush:vb;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
 &lt; dependency&gt;
                             &lt; groupId&gt; org.restlet.jse &lt;/groupId &gt;
                             &lt; artifactId&gt; org.restlet.ext.jetty &lt;/artifactId &gt;
                             &lt; version&gt;2.1.2&lt;/version &gt;
                   &lt;/ dependency&gt;
</pre>
<p>
	再次测试，有效。<br />
	<br />
	值得一提的是，Restlet提供了很多扩展包。会对默认的Restlet服务进行很多增强。<br />
	<br />
	<br />
	&nbsp;</p>

