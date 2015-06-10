---
layout: post
title: Java程序员的堕落
date: 2012-11-02 10:06
author: onecoder
comments: true
categories: [Java, 博文转载, 程序员]
---
<p>
	OneCoder在网上无意看到的一片文章，写的好坏不做评价，略有感触吧。拿来与大家分享。</p>
<p>
	我曾在多个场合撰文说过，现在培训出来的企业Java开发兵团只知道使用某种框架，但完全不知道这些框架的底层原理。今天早上，在依次对三个职位的应聘者面试中，我又再次见到了这种情形。</p>
<p>
	我们的顾问公司收到了一个客户的招聘需求，要一个经验丰富并通晓SQL的Java开发人员。我们公司在这个客户那里享有非常好的声誉，于是我们从招聘代理公司要来了一批简历，开始搜寻合适的目标。</p>
<p>
	首先，关于他们的简历&mdash;&mdash;每份简历都有数页，详细的描述了在各个公司干过的各种工作。每份简历上都有一个技术列表，表面上看这些都是应聘者懂得的技术。下面这个清单是一个真实的简历上记载的各种技术：</p>
<blockquote>
	<p>
		Core Java, J2EE, JSP, JDBC, Servlets, AJAX, XML, HTML, XSLT, Web<br />
		Services, CSS, JavaScript, SQL, Oracle 10g, MySQL 5.0., JMS,Eclipse,<br />
		Adobe Flex Builder 3.x,UML, JDBC, SVN, JUnit, VSS, Jira, HTML, DHTML,<br />
		CSS, AJAX, JavaScript, XML, MXML, Action Script, Servlet, JSP, JSTL,<br />
		Hibernate 3.x, Spring 2.x, IBatis, SOAP, UDDI, WSDL, Apache Axis, Web<br />
		logic Server 8.x, Apache Tomcat 5.0, Struts Framework, MVC, ANT, Maven.</p>
</blockquote>
<p>
	相当的让人印象深刻&hellip;&mdash;&mdash;对于那些从来没有面试过Java程序员的人来说。我并不想说这些应聘者在撒谎，只是这些人跟你的谈话坚持不到三分钟就会在80%的这些技术上露馅。他们可能听说过或者是试过这些技术或工具，这对于他们来说已经足够可以把它们列入简历了。那剩余的他们能说清楚的20%是什么？框架。基本上他们都会向你解释如何配置Struts或Spring，甚至如何让Spring和Hibernate通信。顺便说一句，他们全都喜欢Hibernate，因为它能让你省去了写SQL，而他们对SQL查询语言的知识知之甚少。</p>
<p>
	每当我看到简历上写满了这些Struts，Springs，Hibernates等东西，我就会这样问，&ldquo;假设，你不允许使用任何框架。你要从数据库中的客户表和订单表中取出数据，把它们放到Web页面上，请详细完整的解释一下这整个过程。&rdquo;对大部分人来说，让他们写出一些纯SQL将是一种致命请求&hellip;</p>
<p>
	有个人在简历上写了JQuery。我问她，&ldquo;你为什么要用JQuery&rdquo;&hellip;20秒钟的停顿&hellip;&ldquo;我喜欢它，它很好呀！&rdquo;这就是所有我能从她那挖掘出的关于JQuery的信息。</p>
<p>
	两周前，我参加了一个旧金山JavaOne大会里的一个技术主题会。Brian Goetz向我展示了一些Lambda表达式(也就是闭包)的样例代码，这个东西将在明年的Java8中出现。这是一个相当高级的语言特征，它所用的Java语法是给有勇气的人准备的。我在想，&ldquo;在企业Java世界，谁会去用闭包？10%的程序员？5%？&rdquo;。Java中引入这些表达式只是好玩？只因为它很酷？是因为其它函数式语言中有它？</p>
<p style="text-align: center; ">
	<img alt="" src="http://onecoder.qiniudn.com/8wuliao/CnXpH94y/VCmB1.png" /></p>
<p>
	软件开发产业正在发生变化。它将不再需要更多的程序员。它需要的是高级匠师，能够在软件出问题时配置并替换相关代码。理想情况下，你的团队中应该有一个Java高手，他能真正理解你们应用中的代码，不仅能够从模块级调整它们，并且能理解每行Java代码。这样的人多少也该知道如何写SQL外连接，知道如何纠正编译问题，等等。</p>
<p>
	典型的企业经理都希望手下拥有更多的软件开发人员。多管一些人是向上晋升的一种捷径。是这么回事。但如果你足够聪明，你应该确保你那一堆框架程序员中至少有一个是货真价实的。</p>
<p>
	我又收到了一份简历，将在明天早上10点面试。这简历看起来很眼熟。我只看了它工作过得公司和干的项目。上面其它的信息都是没用的&mdash;&mdash;明天早上10点钟他自然会现出原形。</p>
<p>
	<strong>明天的更新</strong>。现在是早上10点15。又一个面试结束了。第四个错误的回答是，要想把数据传送到浏览器，一个servlet需要把数据放到HTTPSession对象属性里。在听了10分钟这样的回答后，你会认为我终止这次面试的行为很粗鲁吗？</p>
<p>
	<strong>明天的明天的更新</strong>。我给这个职位每小时的报酬上增加了5美元。偏偏这第一个面试者就扛软件开发产业正在发生变化。它将不再需要更多的程序员。它需要的是高级匠师，能够在软件出问题时配置并替换相关代码。理想情况下，你的团队中应该有一个Java高手，他能真正理解你们应用中的代码，不仅能够从模块级调整它们，并且能理解每行Java代码。这样的人多少也该知道如何写SQL外连接，知道如何纠正编译问题，等等。</p>
<p>
	典型的企业经理都希望手下拥有更多的软件开发人员。多管一些人是向上晋升的一种捷径。是这么回事。但如果你足够聪明，你应该确保你那一堆框架程序员中至少有一个是货真价实的。</p>
<p>
	我又收到了一份简历，将在明天早上10点面试。这简历看起来很眼熟。我只看了它工作过得公司和干的项目。上面其它的信息都是没用的&mdash;&mdash;明天早上10点钟他自然会现出原形。</p>
<p>
	明天的更新。现在是早上10点15。又一个面试结束了。第四个错误的回答是，要想把数据传送到浏览器，一个servlet需要把数据放到HTTPSession对象属性里。在听了10分钟这样的回答后，你会认为我终止这次面试的行为很粗鲁吗？</p>
<p>
	明天的明天的更新。我给这个职位每小时的报酬上增加了5美元。偏偏这第一个面试者就扛着胜利的大旗通过了我的面试。别以为这区区5美元就能叩开栖息着有才能的程序员的奇异世界的大门！着胜利的大旗通过了我的面试。别以为这区区5美元就能叩开栖息着有才能的程序员的奇异世界的大门！</p>
<p>
	转自：<a href="http://www.aqee.net/the-degradation-of-java-developers/">http://www.aqee.net/the-degradation-of-java-developers/</a></p>

