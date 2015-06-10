---
layout: post
title: OneCoder翻译:Apache Apollo 介绍: 第一部分
date: 2012-08-24 21:17
author: onecoder
comments: true
categories: [activemq, apollo, 原创翻译, 翻译]
---
<p>
	写在前面，<a href="http://www.coderli.com">OneCoder</a>今天有一种百无聊赖的感觉，写不进去代码，又不忍浪费时间，所以决定看看文章，做做翻译。</p>
<div>
	&nbsp;</div>
<div>
	原文地址：<a href="http://java.dzone.com/articles/introducing-apache-apollo-part">http://java.dzone.com/articles/introducing-apache-apollo-part</a></div>
<div>
	&nbsp;</div>
<div>
	<a href="http://activemq.apache.org/apollo/index.html">Apache Apollo</a>是下一代的ActiveMQ系统。为了在多核的计算机上有更快的处理速度和更大的处理规模，Apollo&ldquo;从头开始&rdquo;重新设计了内核。ActiveMQ是最受欢迎的开源消息中间件（如果不是最受欢迎的，<a href="http://www.coderli.com">OneCoder</a>注：作者意思应该是指如果不仅仅在开源界），并且有很好的性能，消息持久化，高可用性以及网络代理(networks of brokers)等特性。最重要的是，ActiveMQ已经在使用中变得十分稳定，并且我们已经成功使用ActiveMQ支持了如零售、银行、电子上午和政府等领域的世界知名企业。</div>
<div>
	&nbsp;</div>
<div>
	随着ActiveMQ的逐渐壮大， 其内部复杂的线程实现，导致其在高核心，高资源配置的计算机上扩展性不佳。线程实现问题的关键点不在于不合适的线程使用，更多的是因为其设计和开发之初，是处在没有很多可靠的异步Java API和非阻塞的IO库的时期。任何系统在有状态共享，争用同步(contended synchronizations&nbsp;)，独占锁的情况下都容易有扩展性问题。</div>
<div>
	&nbsp;</div>
<div>
	然而，ActiveMQ在其擅长的领域表现尚可，并且由于在现在很稳定，重写其线程架构似乎并不是一个好注意。所以，Apollo诞生了，现在是ActiveMQ的一个子项目。我们仍可以沿用ActiveMQ5.x系列的使用习惯去使用他，不过现在是在新的线程架构之下，并且可以支持未来百万级别的消息目标地址，成千上万的连接，高吞吐率以及对资源的最大程度的利用。特别是，当后端的兼容性完成以后，Apollo便会成为ActiveMQ6.0。</div>
<div>
	&nbsp;</div>
<div>
	对于那些提问说：&ldquo;这是否意味着ActiveMQ的寿命即将终止？&rdquo;的人。斩钉截铁的回答:不！。ActiveMQ在那之前还有很长的路要走，如之前提到的正在被企业领导所使用。ActiveMQ的Bug的修复和支持仍然依赖于FuseSource&nbsp;(不久将会变为RedHat)，当然还有社区。只要对社区的兴趣还在，ActiveMQ(包括其他的Apache项目)就不会消亡。</div>
<div>
	&nbsp;</div>
<div>
	Apollo的目标不是遥不可及，它也不是一个新生的工程。它其实已经开发2年多了，在beta阶段有6个版本，在2012年2月发布了1.0版。已经有不少的人在使用Apollo。Apollo的开发经理&nbsp;<a href="http://hiramchirino.com/blog/">Hiram Chirino</a>发布Apollo与其他消息代理的一些参数指标的对比，如：RabbiMQ,HornetQ以及ActiveMQ。参见&nbsp;<a href="https://github.com/chirino/stomp-benchmark">the stomp benchmarks here</a>&nbsp; 和&nbsp;<a href="https://github.com/chirino/jms-benchmark">the JMS benchmarks</a>.</div>
<div>
	&nbsp;</div>
<div>
	在写这篇文章的时候，Apollo刚刚发布了1.4版本。</div>
<div>
	&nbsp;</div>
<div>
	在第二部分，我们将会关注Apollo的核心线程框架。&nbsp;<a href="http://hawtdispatch.fusesource.org/">hawtdispatch</a>,&nbsp;<a href="http://en.wikipedia.org/wiki/Grand_Central_Dispatch">Grand Central Dispatch</a>以及其他一些新架构带来的扩展性和性能方面的改变。请继续关注：）</div>

