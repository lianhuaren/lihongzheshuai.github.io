---
layout: post
title: Redis作者谈Redis应用场景
date: 2012-07-09 12:36
author: onecoder
comments: true
categories: [Redis, Redis, 场景, 应用]
---
<p>
	一篇老文，不过笔者是新看，用一个东西之前，自然需要弄清楚试用的场景。与您分享：<br />
	以下内容转自：<a href="http://blog.haohtml.com/archives/10139">http://blog.haohtml.com/archives/10139</a><br />
	&nbsp;</p>
<p>
	<a href="http://blog.csdn.net/xymyeah/article/details/6578422">毫无疑问，</a><a href="http://blog.nosqlfan.com/tags/redis" target="_blank" title="查看 Redis 的全部文章">Redis</a>开创了一种新的数据存储思路，使用<a href="http://blog.nosqlfan.com/tags/redis" target="_blank" title="查看 Redis 的全部文章">Redis</a>，我们不用在面对相似功能的数据库时，把精力放在怎么把大象放进冰箱这样的问题上，而是利用<a href="http://blog.nosqlfan.com/tags/redis" target="_blank" title="查看 Redis 的全部文章">Redis</a>灵活多变的数据结构和数据操作，为不同的大象构建不同的冰箱。希望你喜欢这个比喻。</p>
<p>
	下面是一篇新鲜出炉的文章，其作者是Redis作者@<a href="http://twitter.com/antirez" target="_blank">antirez</a>，他描述了Redis比较适合的一些<a href="http://blog.nosqlfan.com/tags/%e5%ba%94%e7%94%a8" target="_blank" title="查看 应用 的全部文章">应用</a>场景，NoSQLFan简单列举在这里，供大家一览：</p>
<p>
	对于对Redis不懂的可以参考redis入门教程:<a href="http://blog.haohtml.com/archives/10174" target="_blank">http://blog.haohtml.com/archives/10174</a></p>
<h3>
	1.取最新N个数据操作</h3>
<p>
	比如典型的取你网站的最新文章，比如通过下面方式，我们可以将最新的5000条评论的ID放在Redis的List集合中，并将超出集合部分从数据库获取</p>
<ul>
	<li>
		使用LPUSH latest.comments&lt;ID&gt;命令，向<a href="http://blog.nosqlfan.com/tags/list" target="_blank" title="查看 list 的全部文章">list</a>集合中插入数据</li>
	<li>
		插入完成后再用LTRIM latest.comments 0 5000命令使其永远只保存最近5000个ID</li>
	<li>
		然后我们在客户端获取某一页评论时可以用下面的逻辑（伪代码）</li>
</ul>
<blockquote>
	<p>
		FUNCTION get_latest_comments(start,num_items):<br />
		&nbsp;&nbsp;&nbsp; id_list = redis.lrange(&quot;latest.comments&quot;,start,start+num_items-1)<br />
		&nbsp;&nbsp;&nbsp; IF id_list.length &lt; num_items<br />
		&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; id_list = SQL_DB(&quot;SELECT ... ORDER BY time LIMIT ...&quot;)<br />
		&nbsp;&nbsp;&nbsp; END<br />
		&nbsp;&nbsp;&nbsp; RETURN id_list<br />
		END</p>
</blockquote>
<p>
	如果你还有不同的筛选纬度，比如某个分类的最新N条，那么你可以再建一个按此分类的List，只存ID的话，Redis是非常高效的。</p>
<h3>
	2.<a href="http://blog.nosqlfan.com/tags/%e6%8e%92%e8%a1%8c%e6%a6%9c" target="_blank" title="查看 排行榜 的全部文章">排行榜</a><a href="http://blog.nosqlfan.com/tags/%e5%ba%94%e7%94%a8" target="_blank" title="查看 应用 的全部文章">应用</a>，取TOP N操作</h3>
<p>
	这个需要与上面需要的不同之处在于，前面操作以时间为权重，这个是以某个条件为权重，比如按顶的次数排序，这时候就需要我们的sorted&nbsp;<a href="http://blog.nosqlfan.com/tags/set" target="_blank" title="查看 set 的全部文章">set</a>出马了，将你要排序的值设置成sorted&nbsp;<a href="http://blog.nosqlfan.com/tags/set" target="_blank" title="查看 set 的全部文章">set</a>的score，将具体的数据设置成相应的value，每次只需要执行一条ZADD命令即可。</p>
<h3>
	3.需要精准设定过期时间的<a href="http://blog.nosqlfan.com/tags/%e5%ba%94%e7%94%a8" target="_blank" title="查看 应用 的全部文章">应用</a></h3>
<p>
	比如你可以把上面说到的<a href="http://blog.nosqlfan.com/tags/sorted-set" target="_blank" title="查看 sorted set 的全部文章">sorted set</a>的score值设置成过期时间的时间戳，那么就可以简单地通过过期时间排序，定时清除过期数据了，不仅是清除Redis中的过期数据，你完全可以把Redis里这个过期时间当成是对数据库中数据的索引，用Redis来找出哪些需要过期删除，然后再精准地从数据库中删除相应的记录。</p>
<h3>
	4.<a href="http://blog.nosqlfan.com/tags/%e8%ae%a1%e6%95%b0%e5%99%a8" target="_blank" title="查看 计数器 的全部文章">计数器</a>应用</h3>
<p>
	Redis的命令都是原子性的，你可以轻松地利用INCR，DECR命令来构建<a href="http://blog.nosqlfan.com/tags/%e8%ae%a1%e6%95%b0%e5%99%a8" target="_blank" title="查看 计数器 的全部文章">计数器</a>系统。</p>
<h3>
	5.Uniq操作，获取某段时间所有数据排重值</h3>
<p>
	这个使用Redis的set数据结构最合适了，只需要不断地将数据将set中扔就行了，set意为集合，所以会自动排重。</p>
<h3>
	6.实时系统，<a href="http://blog.nosqlfan.com/tags/%e5%8f%8d%e5%9e%83%e5%9c%be" target="_blank" title="查看 反垃圾 的全部文章">反垃圾</a>系统</h3>
<p>
	通过上面说到的set功能，你可以知道一个终端用户是否进行了某个操作，可以找到其操作的集合并进行分析统计对比等。没有做不到，只有想不到。</p>
<h3>
	7.Pub/Sub构建实时消息系统</h3>
<p>
	Redis的Pub/Sub系统可以构建实时的消息系统，比如很多用Pub/Sub构建的实时聊天系统的例子。</p>
<h3>
	8.构建<a href="http://blog.nosqlfan.com/tags/%e9%98%9f%e5%88%97" target="_blank" title="查看 队列 的全部文章">队列</a>系统</h3>
<p>
	使用<a href="http://blog.nosqlfan.com/tags/list" target="_blank" title="查看 list 的全部文章">list</a>可以构建<a href="http://blog.nosqlfan.com/tags/%e9%98%9f%e5%88%97" target="_blank" title="查看 队列 的全部文章">队列</a>系统，使用<a href="http://blog.nosqlfan.com/tags/sorted-set" target="_blank" title="查看 sorted set 的全部文章">sorted set</a>可以构建有优先级的队列系统。</p>
<h3>
	9.<a href="http://blog.nosqlfan.com/tags/%e7%bc%93%e5%ad%98" target="_blank" title="查看 缓存 的全部文章">缓存</a></h3>
<p>
	这个不必说了，性能优于Mem<a href="http://blog.nosqlfan.com/tags/cache" target="_blank" title="查看 cache 的全部文章">cache</a>d，数据结构更多样化。</p>
<p>
	来源：<a href="http://antirez.com/post/take-advantage-of-redis-adding-it-to-your-stack.html" target="_blank">antirez.com</a></p>
<p>
	Redis内存存储结构分析 :<a href="http://www.searchtb.com/2011/05/redis-storage.html" target="_blank">http://www.searchtb.com/2011/05/redis-storage.html</a></p>

