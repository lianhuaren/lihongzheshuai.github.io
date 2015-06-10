---
layout: post
title: Hadoop不适用的场景和缺点-信息搜集汇总
date: 2012-12-20 21:09
author: onecoder
comments: true
categories: [BigData, hadoop, 应用场景, 缺点]
---
<p>
	<span style="font-family: Helvetica, Tahoma, Arial, sans-serif; line-height: 1.8em; font-size: 14px; text-align: -webkit-auto;">又是一些离散的看似没有什么营养的信息，不过这也是</span><a href="http://www.coderli.com/" style="font-family: Helvetica, Tahoma, Arial, sans-serif; line-height: 1.8em; font-size: 14px; text-align: -webkit-auto; cursor: pointer;">OneCoder</a><span style="font-family: Helvetica, Tahoma, Arial, sans-serif; line-height: 1.8em; font-size: 14px; text-align: -webkit-auto;">在工作中搜集到的一些信息的汇总，您权当&ldquo;软文&rdquo;也未尝不可。</span></p>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto;">
	<div class="blog_content" id="blog_content" style="line-height: 1.8em;">
		<font color="#0000ff">先是一篇阐述数据分析不易使用Hadoop的文章，不过OneCoder感觉，这些理由往往和直接的会被人忽略，根本不足以动摇选择。</font></div>
	<div class="blog_content" id="blog_content" style="line-height: 1.8em; font-family: Helvetica, Tahoma, Arial, sans-serif;">
		<span style="font-family: Tahoma; "><b>Joe 从五个方面分析了为什么数据分析不使用Hadoop的理由：</b></span></div>
</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto;">
	<div>
		&nbsp;</div>
	<div>
		1：&ldquo;Hadoop是一个框架，不是一个解决方案&rdquo;&mdash;&mdash;他认为在解决大数据分析的问题上人们误认为Hadoop可以立即有效工作，而实际上&ldquo;对于简单的查询，它是可以的。但对于难一些的分析问题，Hadoop会迅速败下阵来，因为需要你直接开发Map/Reduce代码。出于这个原因，Hadoop更像是J2EE编程环境而不是商业分析解决方案。&rdquo; 所谓框架意味着你一定要在之上做个性化和业务相关的开发和实现，而这些都需要成本。</div>
	<div>
		&nbsp;</div>
	<div>
		2：&ldquo;Hadoop的子项目Hive和Pig 都不错，但不能逾越其架构的限制。&rdquo;&mdash;&mdash;Joe提出&ldquo;Hive 和Pig 都是帮助非专业工程师快速有效使用Hadoop的完善工具，用于把分析查询转换为常用的SQL或Java Map/Reduce 任务，这些任务可以部署在Hadoop环境中。&rdquo;其中Hive是基于Hadoop的一个数据仓库工具，它可以帮助实现数据汇总、即时查询以及分析存储在Hadoop兼容的文件系统的大型数据集等。而Pig是并行计算的高级数据流语言和执行框架。但作者认为&ldquo;Hadoop的Map/Reduce框架的一些限制，会导致效率低下，尤其是在节点间通信的情况（这种场合需要排序和连接）。&rdquo;</div>
	<div>
		&nbsp;</div>
	<div>
		3：&ldquo;部署是很方便，快捷而且免费，但在后期维护和开发方面成本很高 &rdquo;&mdash;&mdash;Joe不否认&ldquo;工程师可以在一个小时内下载、安装并发布一个简单的查询，因此Hadoop是非常受欢迎的。而且作为没有软件成本的开源项目使得它是替代甲骨文和Teradata的一个非常有吸引力的选择。但是就像很多通用开源框架一样，它并不会完全适配你的业务，因此，要想把开源框架业务化，你就不得不投入开发和维护。&rdquo;Joe 也认为&ldquo;一旦当你进入维护和开发阶段，Hadoop的真正成本就会变得很明显。</div>
	<div>
		&nbsp;</div>
	<div>
		4：&ldquo;对于大数据流水线和汇总非常有效，但对应用于特定的分析来说是非常可怕的。&rdquo;&mdash;&mdash;&ldquo;Hadoop擅长于大量数据的分析和汇总，或把原始数据转化成对另一个应用程序（如搜索或文本挖掘）更有效的东西&lsquo;流水线&rsquo;- 这是它存在的意义。不过，如果你不知道要分析的问题，或如果你想探索数据的模式，Hadoop的很快变得不可收拾。&ldquo;这再次回到了业务本身，框架是为业务服务的，即便是大数据的分析和汇总，也难以脱离其数据的业务特性。所以对于特定的分析，仍然不得不在编程和执行MapReduce代码上花很多时间才能达到目的。</div>
	<div>
		&nbsp;</div>
	<div>
		5：&ldquo;性能除了&lsquo;不好&rsquo;的时候都很好。&rdquo;&mdash;&mdash;&ldquo;当你需要分析大量的数据时，Hadoop允许你通过数千个节点并行计算，这一点上其潜力很大。但是，并非所有的分析工作可以很容易地进行并行处理，尤其是需要当用户交互驱动的分析。&rdquo; 所以要想性能很好，你仍然需要专门为自己要解决的问题而设计和优化相应的Hadoop程序，否则会很慢。&ldquo;因为每个Map/Reduce 任务都要等到之前的工作完成。&rdquo;所以就像关键路径一样，Hadoop执行性能的快慢会取决于其最慢的MapReduce任务。</div>
	<div>
		&nbsp;</div>
	<div>
		Joe最后认为：&ldquo;Hadoop是一个用来做一些非常复杂的数据分析的杰出工具。但是具有讽刺意味的??是，它也是需要大量的编程工作才能得到这些问题的答案。&rdquo; 这一点不止在数据分析应用方面，它其实反映了目前使用开源框架时候不得不面对的选型平衡问题。当你在选型开源框架或代码的时候，既要考虑清楚它能够帮到你多少，节省多少时间和成本，提高多少效率。也要知道由此而产生多少新增的成本，比如工程师的学习成本、开发和维护成本，以及未来的扩展性，包括如果使用的框架升级了，你和你的团队是否要做相应的升级；甚至还要有安全性方面的考虑，毕竟开源框架的漏洞也是众所周知的。</div>
</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto;">
	&nbsp;</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto;">
	<font color="#0000ff">下面这些理由和介绍倒是说的比较中肯，值得参考</font></div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto;">
	<p>
		<b>Hadoop<font color="#444444" face="Arial, Helvetica, sans-serif" size="3"><span style="line-height: 24px;  ">的有所不为</span></font></b></p>
	<p>
		我们不打算花大量的时间来解释何谓Hadoop，因为许多技术文档和媒体报道已对此作了深入介绍。一言以蔽之，知道Hadoop的两个主要组件很重要：一个是用于存储的Hadoop分布式文件系统（HDFS），另一个是MapReduce框架，让你可以对Hadoop里面存储的任何数据执行批量分析任务。值得一提的是，这种数据不一定是结构化数据，这使得Hadoop非常适合分析和处理来自社交媒体、文档和图形等来源的数据：即并不容易适用于行和列的任何数据。</p>
	<p>
		这倒不是说，你无法将Hadoop用于结构化数据。实际上，市面上有许多解决方案可以充分利用Hadoop的这个优点：每TB比较低的存储开支，以便将结构化数据存储在Hadoop中，以取代关系数据库系统（RDBMS）。但是如果你的存储需求不是那么大，那么在Hadoop和RDBMS之间来回转移数据毫无必要。</p>
	<p>
		你不想使用Hadoop的一个方面是事务型数据。顾名思义，事务型数据异常复杂，因为电子商务网站上的交易事务会生成许多步骤，而这些步骤都必须迅速加以实现。这种场景根本就不适合使用Hadoop。Hadoop也不适合用于要求延迟时间极短的结构化数据集，比如当网页由典型的LAMP堆栈中的MySQL数据库来呈现时。这需要速度快，而Hadoop很难满足这样的要求。</p>
	<p>
		<b><font size="3">Hadoop的有所为</font></b></p>
	<p>
		由于批量处理功能，Hadoop应该部署在这些场合：索引编制、模式识别、推荐引擎建立和情绪分析；在所有这些场合下，数据大量生成，存储在Hadoop中，然后最终使用MapReduce函数来进行查询。</p>
	<p>
		但是这并不意味着，Hadoop会取代你数据中心里面目前的组件。恰恰相反，Hadoop会集成到你现有的IT基础设施里面，以便充分利用进入到贵企业的海量数据。</p>
	<p>
		比如说，设想一个相当典型的非Hadoop企业网站在处理商业交易。据Cloudera的教育服务主管Sarah Sproehnle声称，来自其一个客户的流行网站的日志每天晚上都要经历抽取、转换和加载（ETL）的过程&mdash;&mdash;这个过程可能最多耗时3小时，然后把数据存储到数据仓库中。这时，存储过程将被启动，另外过两小时后，被清理的数据将驻留在数据仓库中。不过，最终的数据集将只有原始大小的五分之一&mdash;&mdash;这意味着，就算可以从整个原始数据集获取什么价值，现在这个价值也荡然无存了。</p>
	<p>
		Hadoop集成到这家企业后，情况大为改观：节省了时间和精力。来自Web服务器的日志数据不用经历ETL操作，而是直接被完整地发送到了Hadoop里面的HDFS。然后，对日志数据执行同样的清理过程，现在只使用MapReduce任务。一旦数据清理完毕，随后被发送到数据仓库。但是这个操作要迅速得多，这归因于省去了ETL这一步，加上MapReduce操作速度快。而且，所有数据仍然保存在Hadoop里面，准备回答网站操作人员之后可能提出来的任何额外问题。</p>
	<p>
		想了解Hadoop，有必要明白这个重要的一点：千万不要把它看作是取代你现有的基础设施，而是补充数据管理和存储功能的一种工具。使用Apache Flume或Apache Sqoop之类的工具，你就能把现有系统与Hadoop联系起来，并且对你的数据进行处理&mdash;&mdash;不管数据有多大。Apache Flume可以将数据从RDBMS获取到Hadoop，并将数据从Hadoop获取到RDBMS；Apache Sqoop则可以将系统日志实时抽取到Hadoop。你只要为Hadoop添加节点，就可以执行数据存储和处理任务。</p>
	<hr />
	<p style=" ">
		<span style="color: rgb(0, 0, 255); text-align: -webkit-auto;">粗浅了解一下Hadoop的处理细节，有利于进一步判断、选型。</span></p>
	<p>
		<b>Hadoop 处理特点：</b></p>
	<p>
		1. Hadoop是数据并行，处理串行. 在一个工作中job, 并行只在一个map段和一个reduce段中发生. 但是这两个段不能并行运行, reduce段直到map段完全完成后才能开始。</p>
	<p>
		2.所有被map过程访问的数据都必须被冻结 (也不能有修改发生)，直至整个工作job完成. 这就意味做Hadoop处理数据是在一个面向批处理batch-oriented风格的链条中实现的， 这就注定它不适合在基于流stream-based处理的方式，在流处理中，数据流是持续的必须立即得到及时处理。</p>
	<p>
		3.数据之间联系是通过一个分布式文件系统(HDFS)完成. 延迟会因为网络I/O开销发生了，这种延迟不会成为面向批处理模式的主要问题，在面向批处理模式中，吞吐量才是首要考虑的，但是这意味做Hadoop不适合实现对延迟要求很严格，甚至不允许有延迟发生的在线实时系统。</p>
	<p>
		根据Hadoop以上特点，给出下来Hadoop不适合的场合：</p>
	<p>
		1.需要数据低延迟性的在线数据处理，（比如股票系统，更新需要让所有人及时知道，不能有丝毫延迟。)</p>
	<p>
		2.在一个大数据集中执一个小数据集的随机ad/hoc处理。</p>
	<p>
		3.执行实时/基于流的处理模式，数据持续到达需要立即被处理(如视频等)。</p>
	<p>
		&nbsp;</p>
	<div>
		<hr style="color: rgb(68, 68, 68); font-family: Arial, Helvetica, sans-serif; font-size: 16px; line-height: 24px; " />
		<div>
			<font color="#0000ff">最后是一篇对NoSQL比较全面的描述的翻译的文章，这里节选出了OneCoder最初关心的一段，不过整篇文章都挺有价值，值得一看。地址放在后面。</font></div>
		<div>
			&nbsp;</div>
		<div>
			NoSQL系统不仅舍弃了很多关系数据库中的操作。它还可能不具备关系数据库以下的一些特性：比如通常银行系统中要求的事务保证，一致性保证以及数据可靠性的保证等。事务机制提供了在执行多个命令时的all-or-nothing保证。一致性保证了如果一个数据更新后，那么在其之后的操作中都能看到这个更新。可靠性保证如果一个数据被更新，它就会被写到持久化的存储设备上（比如说磁盘），并且保证在数据库崩溃后数据可恢复。</div>
		<div>
			通过放宽对上述几点特性的要求，NoSQL系统可以为一些非银行类的业务提供以性能换稳定的策略。而同时，对这几点要求的放宽，又使得NoSQL系统能够轻松的实现分片策略，将远远超出单机容量的大量数据分布在多台机器上的。</div>
	</div>
	<div>
		&nbsp;</div>
	<div>
		<div>
			数据模型及操作模型：你的应用层数据模型是行、对象还是文档型的呢？这个系统是否能支持你进行一些统计工作呢？</div>
		<div>
			可靠性：当你更新数据时，新的数据是否立刻写到持久化存储中去了？新的数据是否同步到多台机器上了？</div>
		<div>
			扩展性：你的数据量有多大，单机是否能容下？你的读写量求单机是否能支持？</div>
		<div>
			分区策略：考虑到你对扩展性，可用性或者持久性的要求，你是否需要一份数据被存在多台机器上？你是否需要知道数据在哪台机器上，以及你能否知道。</div>
		<div>
			一致性：你的数据是否被复制到了多台机器上，这些分布在不同点的数据如何保证一致性？</div>
		<div>
			事务机制：你的业务是否需要ACID的事务机制？</div>
		<div>
			单机性能：如果你打算持久化的将数据存在磁盘上，哪种数据结构能满足你的需求（你的需求是读多还是写多）？写操作是否会成为磁盘瓶颈？</div>
		<div>
			负载可评估：对于一个读多写少的应用，诸如响应用户请求的web应用，我们总会花很多精力来关注负载情况。你可能需要进行数据规模的监控，对多个用户的数据进行汇总统计。你的应用场景是否需要这样的功能呢？</div>
		<div>
			尽管后三点在本章没有独立的小节进行描述，但它们和其它几点一样重要，下面就让我们对以上的几点进行阐述。</div>
		<div>
			&nbsp;</div>
		<div>
			重点参考文章：</div>
		<div>
			<a href="http://sishuok.com/forum/blogPost/list/5348.html" style="cursor: pointer; ">http://sishuok.com/forum/blogPost/list/5348.html</a></div>
	</div>
	<div>
		<hr />
	</div>
</div>

