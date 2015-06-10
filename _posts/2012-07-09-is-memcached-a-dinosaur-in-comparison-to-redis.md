---
layout: post
title: Memcached真的过时了吗？
date: 2012-07-09 13:06
author: onecoder
comments: true
categories: [Memcached, Memcached, Redis]
---
<p style="margin: 0px; padding: 0px 0px 10px; color: rgb(56, 56, 56); font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; font-size: 15px; line-height: 25px; background-color: rgb(255, 255, 255); ">
	这两年<span class="wp_keywordlink_affiliate" style="margin: 0px; padding: 0px; "><a href="http://blog.nosqlfan.com/tags/redis" style="margin: 0px; padding: 0px; color: rgb(0, 0, 0); text-decoration: none; border-bottom-width: 1px; border-bottom-style: dotted; border-bottom-color: rgb(204, 204, 204); " target="_blank" title="查看 Redis 的全部文章">Redis</a></span>火得可以，Redis也常常被当作<span class="wp_keywordlink_affiliate" style="margin: 0px; padding: 0px; "><a href="http://blog.nosqlfan.com/tags/memcached" style="margin: 0px; padding: 0px; color: rgb(0, 0, 0); text-decoration: none; border-bottom-width: 1px; border-bottom-style: dotted; border-bottom-color: rgb(204, 204, 204); " target="_blank" title="查看 Memcached 的全部文章">Memcached</a></span>的挑战者被提到桌面上来。关于Redis与Memcached的比较更是比比皆是。然而，Redis真的在功能、性能以及内存使用效率上都超越了Memcached吗？</p>
<p style="margin: 0px; padding: 0px 0px 10px; color: rgb(56, 56, 56); font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; font-size: 15px; line-height: 25px; background-color: rgb(255, 255, 255); ">
	下面内容来自Redis作者在stackoverflow上的一个回答，对应的问题是《<a href="http://stackoverflow.com/questions/2873249/is-memcached-a-dinosaur-in-comparison-to-redis" style="margin: 0px; padding: 0px; color: rgb(0, 0, 0); text-decoration: none; border-bottom-width: 1px; border-bottom-style: dotted; border-bottom-color: rgb(204, 204, 204); " target="_blank">Is memcached a dinosaur in comparison to Redis?</a>》（相比Redis，Memcached真的过时了吗？）</p>
<ul style="margin: 0px 0px 0px 25px; padding: 0px; color: rgb(56, 56, 56); font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; font-size: 15px; line-height: 25px; background-color: rgb(255, 255, 255); ">
	<li style="margin: 0px 0px 5px; padding: 0px; font-size: 14px; line-height: 19px; ">
		<span style="margin: 0px; padding: 0px; color: rgb(0, 128, 0); ">You should not care too much about performances. Redis is faster per core with small values, but memcached is able to use multiple cores with a single executable and TCP port without help from the client. Also memcached is faster with big values in the order of 100k. Redis recently improved a lot about big values (unstable branch) but still memcached is faster in this use case. The point here is: nor one or the other will likely going to be your bottleneck for the query-per-second they can deliver.</span></li>
	<li style="margin: 0px 0px 5px; padding: 0px; font-size: 14px; line-height: 19px; ">
		没有必要过多的关心性能，因为二者的性能都已经足够高了。由于Redis只使用单核，而Memcached可以使用多核，所以在比较上，平均每一个核上Redis在存储小数据时比Memcached性能更高。而在100k以上的数据中，Memcached性能要高于Redis，虽然Redis最近也在存储大数据的性能上进行优化，但是比起Memcached，还是稍有逊色。说了这么多，结论是，无论你使用哪一个，每秒处理请求的次数都不会成为瓶颈。（比如瓶颈可能会在网卡）</li>
	<li style="margin: 0px 0px 5px; padding: 0px; font-size: 14px; line-height: 19px; ">
		<span style="margin: 0px; padding: 0px; color: rgb(0, 128, 0); ">You should care about memory usage. For simple key-value pairs memcached is more memory efficient. If you use Redis hashes, Redis is more memory efficient. Depends on the use case.</span></li>
	<li style="margin: 0px 0px 5px; padding: 0px; font-size: 14px; line-height: 19px; ">
		如果要说内存使用效率，使用简单的key-value存储的话，Memcached的内存利用率更高，而如果Redis采用hash结构来做key-value存储，由于其组合式的压缩，其内存利用率会高于Memcached。当然，这和你的应用场景和数据特性有关。</li>
	<li style="margin: 0px 0px 5px; padding: 0px; font-size: 14px; line-height: 19px; ">
		<span style="margin: 0px; padding: 0px; color: rgb(0, 128, 0); ">You should care about persistence and replication, two features only available in Redis. Even if your goal is to build a cache it helps that after an upgrade or a reboot your data are still there.</span></li>
	<li style="margin: 0px 0px 5px; padding: 0px; font-size: 14px; line-height: 19px; ">
		如果你对数据持久化和数据同步有所要求，那么推荐你选择Redis，因为这两个特性Memcached都不具备。即使你只是希望在升级或者重启系统后缓存数据不会丢失，选择Redis也是明智的。</li>
	<li style="margin: 0px 0px 5px; padding: 0px; font-size: 14px; line-height: 19px; ">
		<span style="margin: 0px; padding: 0px; color: rgb(0, 128, 0); ">You should care about the kind of operations you need. In Redis there are a lot of complex operations, even just considering the caching use case, you often can do a lot more in a single operation, without requiring data to be processed client side (a lot of I/O is sometimes needed). This operations are often as fast as plain GET and SET. So if you don&rsquo;t need just GEt/SET but more complex things Redis can help a lot (think at timeline caching).</span></li>
	<li style="margin: 0px 0px 5px; padding: 0px; font-size: 14px; line-height: 19px; ">
		当然，最后还得说到你的具体应用需求。Redis相比Memcached来说，拥有更多的数据结构和并支持更丰富的数据操作，通常在Memcached里，你需要将数据拿到客户端来进行类似的修改再set回去。这大大增加了网络IO的次数和数据体积。在Redis中，这些复杂的操作通常和一般的GET/SET一样高效。所以，如果你需要缓存能够支持更复杂的结构和操作，那么Redis会是不错的选择。</li>
</ul>
<p style="margin: 0px; padding: 0px 0px 10px; color: rgb(56, 56, 56); font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; font-size: 15px; line-height: 25px; background-color: rgb(255, 255, 255); ">
	来源：<a href="http://stackoverflow.com/questions/2873249/is-memcached-a-dinosaur-in-comparison-to-redis" style="margin: 0px; padding: 0px; color: rgb(0, 0, 0); text-decoration: none; border-bottom-width: 1px; border-bottom-style: dotted; border-bottom-color: rgb(204, 204, 204); " target="_blank">Is memcached a dinosaur in comparison to Redis?</a>（其他人的回答同样值得一看）<br />
	转自：<a href="http://blog.nosqlfan.com/html/3729.html">http://blog.nosqlfan.com/html/3729.html</a></p>

