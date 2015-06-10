---
layout: post
title: JVM对大对象分配内存的特殊处理
date: 2012-08-31 21:13
author: onecoder
comments: true
categories: [Java基础, jvm]
---
<p>
	今天学习看到的一篇不错的研究博客，特此转发出来，一起学习。</p>
<p>
	以下内容转自：<a href="http://liuzhaodong89.iteye.com/blog/1668352">http://liuzhaodong89.iteye.com/blog/1668352</a></p>
<p>
	前段日子在和leader交流技术的时候，偶然听到jvm在分配内存空间给大对象时，如果young区空间不足会直接在old区切一块过去。对于这个结论很好奇，也比较怀疑，所以就上网搜了下，发现还真有这么回事。以下给出具体代码来说明：</p>
<p>
	首先定义好jvm内存各个区域的大小。我设定的是eden区8M，from和to各1M，old区10M，总共20M的空间，参数如下：</p>
<blockquote>
	<p>
		-Xms20M -Xmx20M -Xmn10M -XX:SurvivorRatio=8</p>
</blockquote>
<p>
	紧接着，开始写程序。很简单，就是初始化一个9M的程序，然后用<a href="http://www.coderli.com/jstat-course-example">JStat</a>(<a href="http://www.coderli.com/jstat-course-example">JStat命令使用方法</a>)命令看jdk的内存使用情况。</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
public class App {
	private static final int _1MB = 1024 * 1024;

	/**
	 * VM参数：-verbose:gc -Xms20M -Xmx20M -Xmn10M -XX:SurvivorRatio=8
	 * -XX:PretenureSizeThreshold=3145728
	 */
	public static void main(String[] args) {
		byte[] allocation = new byte[9*_1MB];
		while(true){
			try {
				Thread.sleep(1000);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}
	}
}</pre>
<p>
	然后打成jar，执行。结果如下：</p>
<blockquote>
	<p>
		S0&nbsp;&nbsp;&nbsp;&nbsp; S1&nbsp;&nbsp;&nbsp;&nbsp; E&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; O&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; P&nbsp;&nbsp;&nbsp;&nbsp; YGC&nbsp;&nbsp;&nbsp;&nbsp; YGCT&nbsp;&nbsp;&nbsp; FGC&nbsp;&nbsp;&nbsp; FGCT&nbsp;&nbsp;&nbsp;&nbsp; GCT &nbsp;</p>
	<div>
		0.00&nbsp;&nbsp; 0.00&nbsp; 18.04&nbsp; 90.00&nbsp; 23.08&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 0&nbsp;&nbsp;&nbsp; 0.000&nbsp;&nbsp;&nbsp; 20&nbsp;&nbsp;&nbsp; 0.027&nbsp;&nbsp;&nbsp; 0.027</div>
</blockquote>
<p>
	果然，当对象大小大于eden区的时候会直接扔到old区。但我还不满足与此，于是将对象改大了些，改成了11M。再次尝试发现结果如下：</p>
<blockquote>
	<div>
		Exception in thread &quot;main&quot; java.lang.OutOfMemoryError: Java heap space</div>
	<div>
		at com.taobao.jdkmem.App.main(App.java:17)</div>
</blockquote>
<p>
	到这里结束了么？当然没有:)这个是一个大的完整的对象，当大对象本身是由一连串的小对象组成的时候，会不会不再OOM呢？于是改了代码再次尝试：</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
public class App {
	private static final int _1MB = 1024 * 1024;

	/**
	 * VM参数：-verbose:gc -Xms20M -Xmx20M -Xmn10M -XX:SurvivorRatio=8
	 * -XX:PretenureSizeThreshold=3145728
	 */
	public static void main(String[] args) {
		byte[][] allocation;
		allocation = new byte[11][_1MB]; // 直接分配在老年代中
		while(true){
			try {
				Thread.sleep(1000);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}
	}
}</pre>
<p>
	再次运行，结果如下：</p>
<blockquote>
	<div>
		S0 &nbsp; &nbsp; S1 &nbsp; &nbsp; E &nbsp; &nbsp; &nbsp;O &nbsp; &nbsp; &nbsp;P &nbsp; &nbsp; YGC &nbsp; &nbsp; YGCT &nbsp; &nbsp;FGC &nbsp; &nbsp;FGCT &nbsp; &nbsp; GCT &nbsp;&nbsp;</div>
	<div>
		0.00 &nbsp;38.06 &nbsp;67.68 &nbsp;60.02 &nbsp;23.10 &nbsp; &nbsp; &nbsp;1 &nbsp; &nbsp;0.007 &nbsp; &nbsp;14 &nbsp; &nbsp;0.012 &nbsp; &nbsp;0.019</div>
</blockquote>
<div>
	<p>
		果然，这次居然又被jvm给生吃下去了。不过这次并非所有的都在old区，而是有一部分还在young区里活着。看来jvm还是足够彪悍的。</p>
	<p>
		&nbsp; &nbsp; 由此可见，当出现大对象的时候，jvm用牺牲部分宝贵的old区的方式来保证了整个jvm的正常运转。所以，程序中尽量要避免大对象，如果实在不行，就让大对象活的尽量久些，莫要new一个然后gc掉再new一个再gc，这么爆jvm可不太友好。</p>
	<p>
		&nbsp; &nbsp; 到这里结束了吧？你猜对了，还没有:P既然知道jvm会对大对象申请内存做特殊处理，那么就在琢磨程序员有没有方法干预这个过程呢？答案是有的，就是使用这个参数<em>-XX:PretenureSizeThreshold</em>。这个参数的单位是Byte，其作用是当新对象申请的内存空间大于这个参数值的时候，直接扔到old区。做个试验就证明了:</p>
	<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
public class App {
	private static final int _1MB = 1024 * 1024;

	/**
	 * VM参数：-verbose:gc -Xms20M -Xmx20M -Xmn10M -XX:SurvivorRatio=8
	 * -XX:PretenureSizeThreshold=3145728
	 */
	public static void main(String[] args) {
		byte[] allocation = new byte[4*_1MB];
		while(true){
			try {
				Thread.sleep(1000);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}
	}
}
</pre>
	<p>
		运行命令如下：</p>
	<blockquote>
		<p>
			java -jar -Xms20M -Xmx20M -Xmn10M -XX:SurvivorRatio=8 -XX:PretenureSizeThreshold=3145728 memtest-1.0-SNAPSHOT.jar&nbsp;</p>
	</blockquote>
	<p>
		&nbsp; 我设置的阈值是3M，意味着超过3M的对象会被直接扔到old区。结果是皆大欢喜，新对象直接被扔到了old区：</p>
	<blockquote>
		<div>
			S0 &nbsp; &nbsp; S1 &nbsp; &nbsp; E &nbsp; &nbsp; &nbsp;O &nbsp; &nbsp; &nbsp;P &nbsp; &nbsp; YGC &nbsp; &nbsp; YGCT &nbsp; &nbsp;FGC &nbsp; &nbsp;FGCT &nbsp; &nbsp; GCT &nbsp;&nbsp;</div>
		<div>
			0.00 &nbsp; 0.00 &nbsp;18.04 &nbsp;40.00 &nbsp;23.08 &nbsp; &nbsp; &nbsp;0 &nbsp; &nbsp;0.000 &nbsp; &nbsp; 0 &nbsp; &nbsp;0.000 &nbsp; &nbsp;0.000</div>
	</blockquote>
	<p>
		试验有了结果，自然而然心情愉悦。但这个参数使用时需要慎重，因为fullgc的代价很高，因此old区就显得非常宝贵。除非你真的清楚你在干什么，否则莫要轻易玩这个参数，万一搞个频繁fullgc就玩大了。ok，到此打完收工。</p>
</div>

