---
layout: post
title: 海量存储之一致性和高可用专题(1)
date: 2012-11-20 22:11
author: onecoder
comments: true
categories: [cap, Hadoop, 一致性, 海量存储]
---
<p>
	转自淘宝综合业务博客的海量存储专题一篇好文，剩下的几篇在OneCoder做了阅读会一并转载。自己水平有限，写不出这样水平的文章，读来学习学习也算不错。</p>
<p>
	以下转自：<a href="http://rdc.taobao.com/team/jm/archives/2541">http://rdc.taobao.com/team/jm/archives/2541</a></p>
<p>
	很久木有和大家见面了，因为博主也需要时间来沉淀。。博主也需要学习和思考。。</p>
<p>
	好吧，不多废话，进入正题，今天我们谈的东西是一致性和安全性。</p>
<p>
	一致性这个问题，非常绕，想用语言表述，难度很大，我给别人去讲的时候，一般都是白板，因为白板有类似&ldquo;动画&rdquo;的效果，能够帮助别人理解，但使用文字，就没有办法了，只好要求各位有一定的抽象思维能力，能在自己的脑袋里模拟这种动画吧:)</p>
<p>
	主要会聊到: 简单的双机两阶段提交，hbase强一致三份，vector clock ，paxos思路，paxos改进思路，既然要阐述问题，那我们就需要先给自己画个框框，首先来对这个问题做一个定义。</p>
<p>
	============</p>
<p>
	写在前面，发现不少读者都会用自己以前的2pc知识来套用文章里面提到的2pc改。</p>
<p>
	我想说的是，在这里的两段提交，与大家所理解的两段提交，有一定区别。</p>
<p>
	他是为了满足文中提到的C问题而改过的2pc.所以能够解决一些问题，但也会产生一些新的问题。各位一定要放弃过去对2pc的理解，一步一步的跟着文中的思路走下来，你就会发现他其实不是真正事务中所用到的2pc，而是专门为了同步和高可用改过的2pc协议。</p>
<p>
	============</p>
<p>
	问题描述</p>
<p>
	&nbsp;</p>
<p>
	人们在计算机上面，经常会碰到这样一个需求：如何能够保证一个数据写入到某台或某组机器上，并且计算机返回成功，那么无论机器是否掉电，都能够保证数据不会丢失，并且能够保证数据按照我写入的顺序排列呢？</p>
<p>
	面对这个问题，一般人的最常见思路就是：每次写都必须保证磁盘写成功才算成功就好了嘛。</p>
<p>
	没错，这就是单机一致性的最好诠释，每次写入，都落一次磁盘，就可以保证在单机的数据安全了。</p>
<p>
	那么有人问了，硬盘坏了怎么办？不是还丢了么？没错啊，所以又引入了一种技术，叫做磁盘阵列（这东西，在我们目前的这个一致性里面不去表述，感兴趣的同学可以自己去研究一下）</p>
<p>
	&nbsp;</p>
<p>
	好像说到这，一致性就说完了嘛。。无非也就是保证每次成功的写入，数据不会丢失，并且按照写入的顺序排列，就可以保证数据的一致性了。</p>
<p>
	&nbsp;</p>
<p>
	别急，我们这才要进入正题：</p>
<p>
	如果给你更多的机器，你能做到更安全么？</p>
<p>
	那么，我们来看看，有了更多机器，我们能做到什么？</p>
<p>
	以前，单机的时候，这台机器挂了，服务也就终止了，没有任何方式能够保证在这台机器断电或挂了的时候，他还能服务不是？但如果有更多的机器，那么你就会忽然发现，一台机器挂了，不是还有其他机器么？一个机房里面的所有机器都挂了，不是还有其他机房么？美国被核武器爆菊了以后，不是还有中国的机房么？地球被火星来客毁灭了，不是还有火星机房么？</p>
<p>
	哈，排比了这么多，其实就是想说明，在机器多了以后，人们就可以额外的追求更多的东西了，这东西就是服务的可用性，无论如何，只要有钱，有网络，服务就可用。怎么样？吸引人吧？（不过，可用性这个词在CAP理论里面，不只是指服务可以被访问，还有个很扯淡的属性是延迟，因为延迟这个属性很难被量化定义，所以我一般认为CAP是比较扯淡的。。。）</p>
<p>
	好，我们现在就来重新定义一下我们要研究的问题：</p>
<p>
	寻求一种能够保证，在给定多台计算机，并且他们相互之间由网络相互连通，中间的数据没有拜占庭将军问题（数据不会被伪造）的前提下，能够做到以下两个特性的方法：</p>
<p>
	1)数据每次成功的写入，数据不会丢失，并且按照写入的顺序排列</p>
<p>
	2)给定安全级别（美国被爆菊？火星人入侵？），保证服务可用性，并尽可能减少机器的消耗。</p>
<p>
	我们把这个问题简写为C问题，里面有两个子问题C1,C2.</p>
<p>
	为了阐述一下C问题，我们需要先准备一个基础知识，这知识如此重要而简单，以至于将伴随着每一个分布式问题而出现（以前，也说过这个问题的哦..:) ）</p>
<p>
	假定有两个人，李雷和韩梅梅，假定，李雷让韩梅梅去把隔壁班的电灯关掉，这时候，韩梅梅可能有以下几种反馈：</p>
<p>
	1)&rdquo;好了，关了&rdquo;(成功)</p>
<p>
	2)&rdquo;开关坏了，没法关&rdquo;(失败)</p>
<p>
	3)</p>
<p>
	呵呵，3是什么？韩梅梅被外星人劫持了，消失了。。于是，反馈也没有了。。（无反馈）</p>
<p>
	这是一切网络传递问题的核心，请好好理解哈。。。</p>
<p>
	&nbsp;</p>
<p>
	&mdash;&mdash;&mdash;&mdash;&ndash;准备结束，进入正题&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;</p>
<p>
	&nbsp;</p>
<p>
	两段提交改：</p>
<p>
	&nbsp;</p>
<p>
	首先，我们来看一种最容易想到的方式，2pc变种协议。</p>
<p>
	如果我有两台机器，那么如何能够保证两台机器C问题呢？</p>
<p style="text-align: center;">
	<img alt="" src="http://onecoder.qiniudn.com/8wuliao/CqLUBjuk/if3oB.jpg" /></p>
<p>
	我们假定A是协调者，那么A将某个事件通知给B，B会有以下几种反馈：</p>
<p>
	1.成功，这个可以不表。正常状态</p>
<p>
	2.失败，这个是第二概率出现的事件，比如硬盘满了？内存满了？不符合某些条件？为了解决这个情况，所以我们必须让A多一个步骤，准备，准备意味着如果B失败，那么A也自然不应该继续进行，应该将A的所有已经做得修改回滚，然后通知客户端：错误啦。</p>
<p>
	因此，我们为了能做到能够让A应付B失败的这个情况，需要将同步协议设计为：</p>
<p>
	PrepareA -&gt; Commit B -&gt; Commit A.</p>
<p>
	使用这个协议，就可以保证B就算出现了某些异常情况，数据还能够回滚。</p>
<p>
	我们再看一些异常情况，因为总共就三个步骤，所以很容易可以枚举所有可能出现的问题：</p>
<p>
	我们将最恶心的一种情况排除掉，因为网络无反馈导致的问题，看看其他问题。</p>
<p>
	PA -&gt;C B(b机器挂掉): 也就是说，如果在Commit B这个步骤失败，这时候可以很容易的通过直接回滚在A的修改，并返回前端异常，来满足一致性问题，但可用性有所丧失，因为这次写入是失败的。</p>
<p>
	在这时的可用性呢？ B机器挂掉，对A来说，应该允许提交继续进行。这样才能保证服务可用，否则，只要有任意的一个机器挂掉，整个集群就不可用，这肯定是不符合预期的嘛。</p>
<p>
	PA -&gt; C B -&gt; C A(A机器挂掉) ：这种情况下，Commit A步骤失败，应该做的事情是，在A这个机器重新恢复后，因为自己的状态是P A,所以他必须询问B机器，你提交了没有啊？如果B机器回答：我提交成功了，那么A机器也必须将自己的数据也做提交操作，就能达到一致。</p>
<p>
	在可用性上面，一台机器挂掉，另外一台还是可以用的，那么，自然而然的想法是，去另外一台机器上做尝试。</p>
<p>
	从上面可以看到，因为B机器已经提交了这条记录，所以数据已经是最新了，可以基于最新数据做新的提交和其他操作，是安全的。</p>
<p>
	&nbsp;</p>
<p>
	怎么样？觉得绕不绕？不过还没完呢，我们来看看2pc改的死穴在哪里。。</p>
<p>
	还记得刚开始的时候，我们提到了排除掉了一种最恶心的情况，这就是网络上最臭名昭著的问题，无反馈啊无反馈。。</p>
<p>
	无反馈这个情况，在2pc改中只会在一个地方出现，因为只有一次网络传输过程：</p>
<p>
	A把自己的状态设置为prepare，然后传递消息给B机器，让B机器做提交操作，然后B反馈A结果。这是唯一的一次网络调用。</p>
<p>
	那么，这无反馈意味着什么呢？</p>
<p>
	1.B成功提交</p>
<p>
	2.B 失败（机器挂掉应该被归类于此）</p>
<p>
	3.网络断开</p>
<p>
	&nbsp;</p>
<p>
	更准确的来说，其实从A机器的角度来看这件事，有两类事情是无法区分出来的：</p>
<p>
	1）B机器是挂掉了呢？还是只是网络断掉了？</p>
<p>
	2）要求B做的操作，是成功了呢？还是失败了呢？</p>
<p>
	不要小看这两种情况。。。他意味着两个悲剧的产生。</p>
<p>
	首先，一致性上就出现了问题，无反馈的情况下，无法区分成功还是失败了，于是最安全和保险的方式，就是等着。。。没错，你没看错，就是死等。等到B给个反馈。。。这种在可用性上基本上是0分了。。无论你有多少机器，死等总不是个办法。。</p>
<p>
	然后，可用性也出现了个问题，我们来看看这个著名的&ldquo;脑裂&rdquo;问题吧：</p>
<p>
	A得不到B的反馈，又为了保证自己的可用性，唯一的选择就只好像【P A -&gt;C B(b机器挂掉):】这里面所提到的方法一样：等待一段时间，超时以后，认为B机器挂掉了。于是自己继续接收新的请求，而不再尝试同步给B。又因为可用性指标是如此重要，所以这基本成为了在这种情况下的必然选择，然而，这个选择会带来更大的问题，左脑和右脑被分开了！</p>
<p>
	为什么？我们假定A所在的机房有一组client，叫做client in A. B 机房有一组client 叫做client in B。开始，A是主机，整个结构worked well.</p>
<p style="text-align: center;">
	<img alt="" src="http://onecoder.qiniudn.com/8wuliao/CqLUBAMD/Vo2lN.jpg" />&lsquo;</p>
<p>
	一旦发生断网</p>
<p style="text-align: center;">
	<img alt="" src="http://onecoder.qiniudn.com/8wuliao/CqLUBeJ7/13gFQZ.jpg" /></p>
<p>
	在这种情况下，A无法给B传递信息，为了可用性，只好认为B挂掉了。允许所有client in A 提交请求到自己，不再尝试同步给B.而B与A的心跳也因为断网而中断，他也无法知道，A到底是挂掉了呢？还是只是网络断了，但为了可用性，只好也把自己设置为主机，允许所有client in B写入数据。于是。。出现了两个主机。。。脑裂。</p>
<p>
	&nbsp;</p>
<p>
	这就是两段提交问题解决了什么，以及面临了什么困境。</p>
<p>
	碰到问题，就要去解决，所以，针对一致性问题上的那个&ldquo;死等&rdquo;的萌呆属性，有人提出了三段提交协议，使用增加的一段提交来减少这种死等的情况。不过3PC基本上没有人在用，因为有其他协议可以做到更多的特性的同时又解决了死等的问题，所以3pc我们在这里就不表了。3pc是无法解决脑裂问题的，所以更多的人把3pc当做发展过程中的一颗路旁的小石头。。</p>
<p>
	而针对脑裂，最简单的解决问题的方法，就是引入第三视点，observer。</p>
<p>
	既然两个人之间，直接通过网络无法区分出对方是不是挂掉了，那么，放另外一台机器在第三个机房，如果真的碰到无响应的时候，就去问问observer:对方活着没有啊？就可以防止脑裂问题了。但这种方法是无法解决一致性问题中的死等问题的。。。</p>
<p>
	&nbsp;</p>
<p>
	所以，最容易想到的方式就是，3pc+observer，完美解决双机一致性和安全性问题。</p>
<p>
	&nbsp;</p>
<p>
	后记3317字。nnd我本来以为可以5篇儿纸说完这个问题的。。现在发现刚阐述了很小一部分。。果然一致性和可用性真不是个简单的问题。这个做个专题吧。</p>
