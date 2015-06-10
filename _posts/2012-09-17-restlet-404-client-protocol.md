---
layout: post
title: Restlet一个由Client的Protocol引发的404的问题
date: 2012-09-17 22:01
author: onecoder
comments: true
categories: [404, Coding生活, protocol, restlet]
---
<p>
	今天<a href="http://www.coderli.com">OneCoder</a>遇到一个不大不小的问题。用Restlet开启了一个rest服务，并且在这个服务内部还会访问其他的rest服务，结果遇到404的错误。但是通过浏览器直接访问却可以访问，通过测试用例直接访问也可以，只有在间接通过restlet访问的时候出现这个问题。<br />
	仔细观察控制台信息，发现如下信息：</p>
<blockquote>
	<p>
		WARNING: The protocol used by this request is not declared in the list of client connectors. (HTTP/1.1)</p>
</blockquote>
<p>
	掏出翻墙的Google查询，在restlet项目的讨论区果然发现了相同的提问，也有人给出了很好的解答。概括起来就是需要在component内添加一行代码：</p>
<p>
	&nbsp;</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
component.getClients​().add(Protocol.HTTP​);
</pre>
<p>
	&nbsp;</p>
<p>
	重新启动，问题解决。</p>
<p>
	原帖地址：<a href="http://restlet.tigris.org/ds/viewMessage.do?dsForumId=4447&amp;dsMessageId=2638482">http://restlet.tigris.org/ds/viewMessage.do?dsForumId=4447&amp;dsMessageId=2638482</a></p>

