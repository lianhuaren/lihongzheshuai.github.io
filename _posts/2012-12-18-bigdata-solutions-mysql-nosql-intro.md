---
layout: post
title: 海量数据问题和解决方案搜集汇总
date: 2012-12-18 23:06
author: onecoder
comments: true
categories: [BigData, NoSQL, 关系性数据库, 海量数据, 解决方案]
---
<p>
	<a href="http://www.coderli.com">OneCoder</a>搜集的一些关于海量数据的解决方案和优缺点吧，说实话没什么实际内容，不过作为一些粗浅的理解，也许是有那么一丁点帮助的。</p>
<div>
	&nbsp;</div>
<div>
	<strong>传统关系型数据的问题</strong></div>
<div>
	&nbsp;</div>
<div>
	1. 扩展困难：由于存在类似Join这样多表查询机制，使得数据库在扩展方面很艰难;&nbsp;<br />
	2. 读写慢：这种情况主要发生在数据量达到一定规模时由于关系型数据库的系统逻辑非常复杂，使得其非常容易发生死锁等的并发问题，所以导致其读写速度下滑非常严重;&nbsp;<br />
	3. 成本高：企业级数据库的License价格很惊人，并且随着系统的规模，而不断上升;&nbsp;<br />
	4. 有限的支撑容量：现有关系型解决方案还无法支撑Google这样海量的数据存储;&nbsp;</div>
<div>
	&nbsp;</div>
<div>
	<strong>MySQL海量数据解决方案</strong></div>
<div>
	&nbsp;</div>
<div>
	1.&nbsp;数据切分，库外路由，或者库内切分表。可考虑切分方式：</div>
<div>
	&nbsp;</div>
<div>
	(1) user_id为区分，1～1000的对应DB1，1001～2000的对应DB2，以此类推；</div>
<div>
	<span style="color:#ff0000;">优点：可部分迁移</span></div>
<div>
	<p>
		<span style="color:#008000;">缺点：数据分布不均</span></p>
	<p>
		(2)hash取模分：</p>
	<p>
		对user_id进行hash（或者如果user_id是数值型的话直接使用user_id 的值也可），然后用一个特定的数字，比如应用中需要将一个数据库切分成4个数据库的话，我们就用4这个数字对user_id的hash值进行取模运算，也就是user_id%4,这样的话每次运算就有四种可能：结果为1的时候对应DB1；结果为2的时候对应DB2；结果为3的时候对应DB3；结果为0的时候对应DB4，这样一来就非常均匀的将数据分配到4个DB中。</p>
	<p>
		<span style="color:#ff0000;">优点：数据分布均匀</span></p>
	<p>
		<span style="color:#008000;">缺点：数据迁移的时候麻烦，不能按照机器性能分摊数据</span></p>
	<p>
		(3)在认证库中保存数据库配置</p>
	<p>
		就是建立一个DB，这个DB单独保存user_id到DB的映射关系，每次访问数据库的时候都要先查询一次这个数据库，以得到具体的DB信息，然后才能进行我们需要的查询操作。</p>
	<p>
		<span style="color:#ff0000;">优点：灵活性强，一对一关系</span></p>
	<p>
		<span style="color:#008000;">缺点：每次查询之前都要多一次查询，性能大打折扣</span></p>
</div>
<div>
	2. 数据库集群，读写分离，数据切分。</div>
<div>
	&nbsp;</div>
<div>
	&nbsp;&nbsp;<img alt="" data-inited="true" data-media-type="image" distinguish="B356092027384C72B30E20D2B93EFBC4" src="http://onecoder.qiniudn.com/8wuliao/Cv2lBCAg/11baN.png" title="【转】mysql &lt;wbr&gt;海量数据的存储和访问解决方案" /></div>
<div>
	&nbsp; &nbsp; 在上述数据切分的基础上，以Group为基础，做集群，提高可用性，同时可以做到读写分离。</div>
<div>
	&nbsp;</div>
<div>
	<strong>Oracle海量数据解决方案</strong></div>
<div>
	1. RAC（Oracle Real Application Cluster）</div>
<div>
	<span style="color:#ff0000;">优势：</span></div>
<div>
	<div>
		<span style="color:#ff0000;">（1）可以实现多个节点间的负载均衡</span></div>
	<div>
		RAC数据库集群可以根据设定的调度策略，在集群节点间实现负载均衡，因此，RAC数据库每个节点都是工作的，同时也处于互相监控状态，当某个节点出现故障时，RAC集群自动将失败节点从集群隔离，并将失败节点的请求自动转移到其它健康节点上，从而实现服务透明切换。</div>
	<div>
		<span style="color:#ff0000;">（2）可以提供高可用服务</span></div>
	<div>
		这个是Oracle Clusterware实现的功能，通过CRS可以实现节点状态监控，故障透明转移，这保证了oracle数据库可以对外不间断的提供服务。</div>
	<div>
		<span style="color:#ff0000;">（3）通过横向扩展提高了并发连接数</span></div>
	<div>
		RAC这个优点非常适合大型的联机事务系统中。</div>
	<div>
		<span style="color:#ff0000;">（4）通过并行执行技术提高了事务响应时间</span></div>
	<div>
		这个是RAC集群的一大优势，通常用于数据分享系统中。</div>
	<div>
		<span style="color:#ff0000;">（5）具有很好的扩展性</span></div>
	<div>
		在集群系统不能满足繁忙的业务系统时，RAC数据库可以很方便的添加集群节点，且可以在线完成节点的添加，并自动加入集群系统，不存在宕机时间；同时在不需要某个集群节点时，删除节点也非常简单。</div>
</div>
<div>
	&nbsp;</div>
<div>
	<span style="color:#008000;">缺点：</span></div>
<div>
	<div>
		（1）与单机数据库相比，管理维护更复杂，并对维护人员要求更高</div>
	<div>
		（2）底层规划设计不好时，系统整体性能会较差，甚至不如单机系统的性能。所以，如果对RAC数据库不是很了解，不建议马上在生产环境中使用。</div>
	<div>
		（3）由于RAC集群系统需要多个节点，那么需要购买多台服务器，同时需要oracle企业级版本数据库，这无形中也增加了软硬件成本。</div>
	<div>
		&nbsp;</div>
</div>
<div>
	<strong>NoSQL数据库到底存在哪些优缺点</strong></div>
<div>
	&nbsp;</div>
<div>
	在优势方面，主要体现在下面这三点：&nbsp;<br />
	1. 简单的扩展：典型例子是Cassandra，由于其架构是类似于经典的P2P，所以能通过轻松地添加新的节点来扩展这个集群;&nbsp;<br />
	2. 快速的读写：主要例子有Redis，由于其逻辑简单，而且纯内存操作，使得其性能非常出色，单节点每秒可以处理超过10万次读写操作;&nbsp;<br />
	3. 低廉的成本：这是大多数分布式数据库共有的特点，因为主要都是开源软件，没有昂贵的License成本;&nbsp;<br />
	&nbsp;</div>
<div>
	NoSQL数据库还存在着很多的不足，常见主要有下面这几个：&nbsp;<br />
	1. 不提供对SQL的支持：如果不支持SQL这样的工业标准，将会对用户产生一定的学习和应用迁移成本;&nbsp;<br />
	2. 支持的特性不够丰富：现有产品所提供的功能都比较有限，大多数NoSQL数据库都不支持事务，也不像MS SQL Server和Oracle那样能提供各种附加功能，比如BI和报表等;&nbsp;<br />
	3. 现有产品的不够成熟：大多数产品都还处于初创期，和关系型数据库几十年的完善不可同日而语;&nbsp;</div>

