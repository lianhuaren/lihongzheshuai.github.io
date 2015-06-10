---
layout: post
title: Cassandra 键空间(KeySpaces)简介
date: 2012-10-30 10:36
author: onecoder
comments: true
categories: [Cassandra, Keyspaces, 原创翻译, 翻译]
---
<p>
	很久没更新了，原因一言难尽。还是继续看看文章，做做翻译吧。路，看不清。</p>
<div>
	原文地址：<a href="http://www.datastax.com/docs/1.1/ddl/keyspaces">http://www.datastax.com/docs/1.1/ddl/keyspaces</a></div>
<div>
	&nbsp;</div>
<div>
	在Cassandra中，键空间是你的应用数据的容器，跟关系型数据库中的schema类似。键空间用来分组列族。特别的，对每个应用来说，一族只有一个键空间。</div>
<div>
	&nbsp;</div>
<div>
	在每个键空间上对复制有控制要求，所以有不同复制要求的数据需要存储在不同的键空间上。键空间不是被设计用来作为数据模型内部映射层的标记，而仅仅是作为一种对于一组列族来说控制数据复制的方式。</div>
<div>
	&nbsp;</div>
<div>
	定义键空间</div>
<div>
	&nbsp;</div>
<div>
	许多客户端工具可以选择来基于数据定义语言(DDL)命令来定义和调整键空间，如Cassandra CLI和CQL。例如，在CQL中定义键空间：</div>
<div>
	<pre class="brush:shell;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
CREATE KEYSPACE keyspace_name WITH
strategy_class = &#39;SimpleStrategy&#39;
AND strategy_options:replication_factor=2;
</pre>
</div>
<div>
	<a href="http://www.coderli.com">OneCoder</a>注：文档中，这节很短，说实话OneCoder也没收获到太多的东西，下一节是关于列族的介绍，相对比较长，相信结合下一节和前面给出的图形，可以让你对Cassandra的数据结构有一个更好的理解。</div>
<div>
	引用一段描述：</div>
<div>
	<blockquote>
		<p>
			Keyspace</p>
		<p>
			Cassandra中的最大组织单元，里面包含了一系列Column family，Keyspace一般是应用程序的名称。你可以把它理解为Oracle里面的一个schema，包含了一系列的对象。</p>
	</blockquote>
</div>
<p>
	&nbsp;</p>

