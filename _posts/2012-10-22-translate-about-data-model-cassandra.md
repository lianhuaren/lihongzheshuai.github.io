---
layout: post
title: Apache Cassandra 数据模型介绍
date: 2012-10-22 13:46
author: onecoder
comments: true
categories: [Cassandra, dataModel, hadoop, 原创翻译, 翻译]
---
<p>
	每周一译确实感觉很有收获，<a href="http://www.coderli.com">OneCoder</a>非常乐于坚持。由于可能要在11月底去参加Hadoop大会，之前又没怎么接触过，先多了解一些吧。</p>
<p>
	翻译的原文来自Cassandra的官方文档：<a href="http://www.datastax.com/docs/1.1/ddl/about-data-model">http://www.datastax.com/docs/1.1/ddl/about-data-model</a></p>
<div>
	对于刚从关系型数据库转来的Cassandra的开发者来说，Cassandra的数据模型可能有一些困扰。下面的章节，会对这两种数据模型进行对比。</div>
<div>
	&nbsp;</div>
<div>
	Cassandra数据模型和关系型数据库模型对比<br />
	<div>
		&nbsp;</div>
	<div>
		Cassandra的数据模型是为大规模分布式数据设计的。不像关系型数据库根据实体和关系建模，Cassandra数据模型的设计是基于你希望进行的查询来的。尽管想对Cassandra数据模型和关系型数据库模型进行对比这个想法很自然，不过他们确实很不同。在关系型数据库中，数据存储在表中并且表中包含的数据相互之间有关联。数据一般规范是减少冗余实体，并且表通过键建立关联来满足查询的需要。</div>
	<div>
		&nbsp;</div>
	<div>
		例如，考虑一个简单的用户创建Blog的例子。在这个应用中，blog的列表是根据主题的分类(体育，流行等等)。用户也可以选择订阅启用用户的博文。在这个例子中，user_id 是用户表的主键并且是blog和订阅表的外键。使用这个关联模型，可以各个表中使用关联的SQL查询解决下列问题：</div>
	<div>
		<ul>
			<li>
				哪些用户订阅我的blog？</li>
			<li>
				列出所有fashion类型的blog列表。</li>
			<li>
				列出我订阅的blog中最新的日志。</li>
		</ul>
		<div style="text-align: center; ">
			<img alt="" src="http://onecoder.qiniudn.com/8wuliao/Cmj5vGLN/y7rw2.png" /></div>
	</div>
	<div>
		&nbsp;</div>
	<div>
		在Cassandra中，键空间(keyspace)是你应用数据的容器，跟关系型数据库中的库或者schema类型。在键空间中是一个或者多个列族对象，跟表类似。列族包含列，一组相关的列由应用提供的行键标识。一个列族中的每一行不要求有相同的列。</div>
	<div>
		&nbsp;</div>
	<div>
		Cassandra不强制要求列族间拥有跟关系型数据表之间类似的关联关系：在Cassandra中没有外键，列族的关联查询是不支持的。每个列族有自己独立的一组列，这组列在你应用的特定查询时一般一起被访问的。事实上，一个好的规划是，一个查询对应一个列族以最优化你的列族读取性能。</div>
	<div>
		&nbsp;</div>
	<div>
		还以blog应用为例，你应该有一个跟关系型数据类似的用户数据和blog文章的列族。其他的列族（或者第二索引）可以用来支持你应用所需的查询。例如，支持上面列到的查询操作。你需要设计其他的列族去支持上述查询。记住，你需要一些反范式的数据设计。<br />
		<div>
			<div style="text-align: center; ">
				<img alt="" src="http://onecoder.qiniudn.com/8wuliao/Cmj5uH4X/NzZif.png" /></div>
			<div>
				&nbsp;</div>
		</div>
	</div>
</div>

