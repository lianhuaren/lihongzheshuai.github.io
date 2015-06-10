---
layout: post
title: OneCoder翻译-MySQL 的不良用法 Group By
date: 2012-08-19 22:37
author: onecoder
comments: true
categories: [group by, mysql, 原创翻译, 翻译]
---
<p>
	<a href="http://www.coderli.com">OneCoder</a>最近打算坚持每周左右至少翻译一篇技术文章，不管是从学习技术和学习英语的角度，对自己都会是一个提高。<br />
	水平有限，如果您觉得翻译太过粗糙，甚至错误，还望不吝指出，<a href="http://www.coderli.com">OneCoder</a>必将虚心接受，努力学习改进。</p>
<p>
	MySQL是一个&ldquo;容错性&rdquo;很强的数据库。这种&ldquo;容错性&rdquo;体现在既能作为生产环境中的关系型数据库，又深受那些并不真正回写SQL的各色hacker欢迎。也正式因为他们不是真正会写SQL，所以他们选择了MySQL，因为MySQL容错性高。这种容错性，就跟在他们最喜欢的PHP语言中的&ldquo;magic quotes&rdquo;特性一样。MySQL可以允许你写&ldquo;错误&rdquo;的SQL语言，仍可以运行它。我所说的&ldquo;错误的&rdquo;SQL是这样的：</p>
<pre class="brush:sql;first-line:1;pad-line-numbers:false;highlight:null;collapse:false;">
SELECT o.custid, c.name, MAX(o.payment)
  FROM orders AS o, customers AS c
  WHERE o.custid = c.custid
  GROUP BY o.custid;
</pre>
<p>
	这条语句是出在MySQL的官方文档：</p>
<div>
	<a href="http://dev.mysql.com/doc/refman/5.6/en/group-by-hidden-columns.html">http://dev.mysql.com/doc/refman/5.6/en/group-by-hidden-columns.html</a>（<a href="http://www.coderli.com">OneCoder</a>注：应该看看这个文档的介绍，能更清楚的明白该文的作者想表达的意思。）</div>
<div>
	&nbsp;</div>
<div>
	那么，这条语句的含义是怎样呢。c.name这个映射的返回结果是什么？最大的(c.name)？任意的(c.name)？还是第一个(c.name)？还是NULL？还是42？根据文档，任意的c.name(ANY(c.name))，应该是最合理的可能。这种特殊的语法大概对那些少数的真正了解这种语法什么时候是有用的人是非常的智能的一种方式。也就是当他们确切的知道，o.custid和c.name需要是1对1的关系是，他们可以省略掉一些没有必要的语句，例如Max(c.name)或者在Group by 子句中加上c.name。（是的，节省了8个字符。）</div>
<div>
	&nbsp;</div>
<div>
	但是，对大部分MySQL的初学者来说，会对此感到迷惑：</div>
<div>
	<ul>
		<li>
			首先，他们会迷惑于并没有得到预期的c.name值。</li>
		<li>
			其次，当他们最终将要迁移到另一个数据库的时候，会再次遇到困境。会报一些有趣的语法错误，如：ORA-00979 not a GROUP BY expression。</li>
	</ul>
	<div>
		所以：</div>
</div>
<div>
	<ul>
		<li>
			MySQL的用户，停止使用这个特性。他直接带来痛苦和折磨，尽管你知道他是如何工作的。SQL语句中的GROUP BY不是被设计用于这种工作方式的。</li>
		<li>
			MySQL数据库：废弃这个特性。</li>
	</ul>
</div>
<div>
	&nbsp;</div>
<div>
	&nbsp;</div>
<div>
	原文地址：<a href="http://java.dzone.com/articles/mysql-bad-idea-384">http://java.dzone.com/articles/mysql-bad-idea-384</a></div>

