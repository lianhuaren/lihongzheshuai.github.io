---
layout: post
title: OneCoder翻译：Apache Hadoop简介
date: 2012-09-20 20:20
author: onecoder
comments: true
categories: [hadoop, 原创翻译, 翻译]
---
<p>
	最近忙着搬家前前后后的事儿，翻看博客，发现上周的翻译作业尚未完成。正好要学习一下Hadoop的知识，所以翻译一下Hadoop官网的一些介绍和文档。</p>
<div>
	原文地址：<a href="http://hadoop.apache.org/">http://hadoop.apache.org/</a></div>
<div>
	&nbsp;</div>
<div>
	Apache&trade; Hadoop&reg;&nbsp;是一个开源的稳定可扩展的分布式计算系统。</div>
<div>
	&nbsp;</div>
<div>
	Apache Hadoop软件库是一个可以让你用简单的程序模型，通过一组计算机对大数据量进行分布式运算的框架。它在设计上允许你从单一节点扩展到成百上千的节点，每个节点独立提供计算和存储资源。Hadoop不完全依靠硬件资源来保证搞可用性，它可以在应用层发现并处理错误，所以，它的高可用性是在计算机集群之上的，集群内的每个计算机都是可以出错的。</div>
<div>
	&nbsp;</div>
<div>
	Hadoop工程包括以下模块：</div>
<div>
	<ul>
		<li>
			<strong>Hadoop Common</strong>：支持其他Hadoop模块的基础模块。</li>
		<li>
			<strong>Hadoop Distributed File System (HDFS&trade;)</strong>: 提供对应用数据高吞吐量访问的分布式文件系统。</li>
		<li>
			<strong>Hadoop YARN</strong>: 任务调度和集群资源管理框架。</li>
		<li>
			<strong>Hadoop MapReduce</strong>: 基于YARN的大数据量并行计算系统。</li>
	</ul>
	<div>
		Apache旗下其他Hadoop相关的工程包括：</div>
	<div>
		<ul>
			<li>
				<a href="http://avro.apache.org/">Avro&trade;</a>: 数据序列化系统。</li>
			<li>
				<a href="http://cassandra.apache.org/">Cassandra&trade;</a>: 可扩展的，无单点故障的多主控数据库。</li>
			<li>
				<a href="http://incubator.apache.org/chukwa/">Chukwa&trade;</a>:&nbsp;用于管理大规模分布式系统的数据采集系统。</li>
			<li>
				<a href="http://hbase.apache.org/">HBase&trade;</a>: 可扩展的分布式数据库，用于大表的结构化数据存储。</li>
			<li>
				<a href="http://hive.apache.org/">Hive&trade;</a>:&nbsp;基础数据仓库，提供数据摘要和随机查询。</li>
			<li>
				<a href="http://mahout.apache.org/">Mahout&trade;</a>:&nbsp;可扩展的机器学习(Machine Learning)和数据挖掘库。</li>
			<li>
				<a href="http://pig.apache.org/">Pig&trade;</a>:&nbsp;用于并行计算的高级数据流语言和执行框架。</li>
			<li>
				<a href="http://zookeeper.apache.org/">ZooKeeper&trade;</a>:&nbsp;用于分布式应用的高性能协调服务。</li>
		</ul>
	</div>
</div>

