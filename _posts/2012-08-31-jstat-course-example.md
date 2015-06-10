---
layout: post
title: JStat命令使用方法
date: 2012-08-31 23:13
author: onecoder
comments: true
categories: [Java基础, jstat, jvm]
---
<p>
	也是今天看到的一篇很好的学习文章，是OneCoder在学习《<a href="http://www.coderli.com/jvm-young-old-eden" rel="bookmark">JVM对大对象分配内存的特殊处理</a>》过程中，针对其中提到的jstat命令找到的一篇很好的教程。OneCoder根据这个教程学会jstat命令的基本使用，拿出与大家分享。</p>
<p>
	以下内容转自：<a href="http://xueliang880107.iteye.com/blog/954073">http://xueliang880107.iteye.com/blog/954073</a></p>
<div>
	用以判断JVM是否存在内存问题呢？如何判断JVM垃圾回收是否正常？一般的top指令基本上满足不了这样的需求，因为它主要监控的是总体的系统资源，很难定位到java应用程序。</div>
<div>
	&nbsp;</div>
<div>
	Jstat是JDK自带的一个轻量级小工具。全称&ldquo;Java Virtual Machine statistics monitoring tool&rdquo;，它位于java的bin目录下，主要利用JVM内建的指令对Java应用程序的资源和性能进行实时的命令行的监控，包括了对Heap size和垃圾回收状况的监控。可见，Jstat是轻量级的、专门针对JVM的工具，非常适用。由于JVM内存设置较大，图中百分比变化不太明显</div>
<div>
	&nbsp;</div>
<div>
	一个极强的监视VM内存工具。可以用来监视VM内存内的各种堆和非堆的大小及其内存使用量。</div>
<div>
	&nbsp;</div>
<div>
	jstat工具特别强大，有众多的可选项，详细查看堆内各个部分的使用量，以及加载类的数量。使用时，需加上查看进程的进程id，和所选参数。</div>
<div>
	&nbsp;</div>
<div>
	执行：cd $JAVA_HOME/bin中执行jstat，注意jstat后一定要跟参数。</div>
<div>
	&nbsp;</div>
<div>
	jstat ：对VM内存使用量进行监控。</div>
<div>
	&nbsp; &nbsp; jstat工具特别强大，有众多的可选项，详细查看堆内各个部分的使用量，以及加载类的数量。使用时，需加上查看进程的进程id，和所选参数。以下详细介绍各个参数的意义。</div>
<blockquote>
	<div>
		&nbsp; &nbsp; jstat -class pid:显示加载class的数量，及所占空间等信息。</div>
	<div>
		&nbsp; &nbsp; jstat -compiler pid:显示VM实时编译的数量等信息。</div>
	<div>
		&nbsp; &nbsp; jstat -gc pid:可以显示gc的信息，查看gc的次数，及时间。其中最后五项，分别是young gc的次数，young gc的时间，full gc的次数，full gc的时间，gc的总时间。</div>
	<div>
		&nbsp; &nbsp; jstat -gccapacity:可以显示，VM内存中三代（young,old,perm）对象的使用和占用大小，如：PGCMN显示的是最小perm的内存使用量，PGCMX显示的是perm的内存最大使用量，PGC是当前新生成的perm内存占用量，PC是但前perm内存占用量。其他的可以根据这个类推， OC是old内纯的占用量。</div>
	<div>
		&nbsp; &nbsp; jstat -gcnew pid:new对象的信息。</div>
	<div>
		&nbsp; &nbsp; jstat -gcnewcapacity pid:new对象的信息及其占用量。</div>
	<div>
		&nbsp; &nbsp; jstat -gcold pid:old对象的信息。</div>
	<div>
		&nbsp; &nbsp; jstat -gcoldcapacity pid:old对象的信息及其占用量。</div>
	<div>
		&nbsp; &nbsp; jstat -gcpermcapacity pid: perm对象的信息及其占用量。</div>
	<div>
		&nbsp; &nbsp; jstat -util pid:统计gc信息统计。</div>
	<div>
		&nbsp; &nbsp; jstat -printcompilation pid:当前VM执行的信息。</div>
</blockquote>
<div>
	&nbsp; &nbsp; 除了以上一个参数外，还可以同时加上 两个数字，如：jstat -printcompilation 3024 250 6是每250毫秒打印一次，一共打印6次，还可以加上-h3每三行显示一下标题。</div>
<div>
	&nbsp;</div>
<div>
	语法结构：</div>
<blockquote>
	<div>
		Usage: jstat -help|-options</div>
	<div>
		&nbsp;</div>
	<div>
		&nbsp; &nbsp; &nbsp; &nbsp;jstat -&lt;option&gt; [-t] [-h&lt;lines&gt;] &lt;vmid&gt; [&lt;interval&gt; [&lt;count&gt;]]</div>
	<div>
		&nbsp;</div>
	<div>
		&nbsp;参数解释：</div>
	<div>
		&nbsp;</div>
	<div>
		Options &mdash; 选项，我们一般使用 -gcutil 查看gc情况</div>
	<div>
		&nbsp;</div>
	<div>
		vmid &nbsp; &nbsp;&mdash; VM的进程号，即当前运行的java进程号</div>
	<div>
		&nbsp;</div>
	<div>
		interval&ndash; 间隔时间，单位为秒或者毫秒</div>
	<div>
		&nbsp;</div>
	<div>
		count &nbsp; &mdash; 打印次数，如果缺省则打印无数次</div>
	<div>
		&nbsp;</div>
	<div>
		S0 &nbsp;&mdash; Heap上的 Survivor space 0 区已使用空间的百分比</div>
	<div>
		S1 &nbsp;&mdash; Heap上的 Survivor space 1 区已使用空间的百分比</div>
	<div>
		E &nbsp; &mdash; Heap上的 Eden space 区已使用空间的百分比</div>
	<div>
		O &nbsp; &mdash; Heap上的 Old space 区已使用空间的百分比</div>
	<div>
		P &nbsp; &mdash; Perm space 区已使用空间的百分比</div>
	<div>
		YGC &mdash; 从应用程序启动到采样时发生 Young GC 的次数</div>
	<div>
		YGCT&ndash; 从应用程序启动到采样时 Young GC 所用的时间(单位秒)</div>
	<div>
		FGC &mdash; 从应用程序启动到采样时发生 Full GC 的次数</div>
	<div>
		FGCT&ndash; 从应用程序启动到采样时 Full GC 所用的时间(单位秒)</div>
	<div>
		GCT &mdash; 从应用程序启动到采样时用于垃圾回收的总时间(单位秒)</div>
</blockquote>
<div>
	实例使用1：</div>
<blockquote>
	<div>
		[root@localhost bin]# jstat -gcutil 25444</div>
	<div>
		&nbsp;</div>
	<div>
		&nbsp; S0 &nbsp; &nbsp; S1 &nbsp; &nbsp; E &nbsp; &nbsp; &nbsp;O &nbsp; &nbsp; &nbsp;P &nbsp; &nbsp; YGC &nbsp; &nbsp; YGCT &nbsp; &nbsp;FGC &nbsp; &nbsp;FGCT &nbsp; &nbsp; GCT</div>
	<div>
		&nbsp;</div>
	<div>
		&nbsp;11.63 &nbsp; 0.00 &nbsp; 56.46 &nbsp;66.92 &nbsp;98.49 162 &nbsp; &nbsp;0.248 &nbsp; &nbsp;6 &nbsp; &nbsp; &nbsp;0.331 &nbsp; &nbsp;0.579</div>
</blockquote>
<div>
	&nbsp;</div>
<div>
	实例使用2：(25444是java的进程号，ps -ef | grep java)</div>
<blockquote>
	<div>
		[root@localhost bin]# jstat -gcutil 25444 1000 5</div>
	<div>
		&nbsp;</div>
	<div>
		&nbsp; S0 &nbsp; &nbsp; S1 &nbsp; &nbsp; E &nbsp; &nbsp; &nbsp;O &nbsp; &nbsp; &nbsp;P &nbsp; &nbsp; YGC &nbsp; &nbsp; YGCT &nbsp; &nbsp;FGC &nbsp; &nbsp;FGCT &nbsp; &nbsp; GCT</div>
	<div>
		&nbsp;</div>
	<div>
		&nbsp;73.54 &nbsp; 0.00 &nbsp;99.04 &nbsp;67.52 &nbsp;98.49 &nbsp; &nbsp;166 &nbsp; &nbsp;0.252 &nbsp; &nbsp; 6 &nbsp; &nbsp;0.331 &nbsp; &nbsp;0.583</div>
	<div>
		&nbsp;</div>
	<div>
		&nbsp;73.54 &nbsp; 0.00 &nbsp;99.04 &nbsp;67.52 &nbsp;98.49 &nbsp; &nbsp;166 &nbsp; &nbsp;0.252 &nbsp; &nbsp; 6 &nbsp; &nbsp;0.331 &nbsp; &nbsp;0.583</div>
	<div>
		&nbsp;</div>
	<div>
		&nbsp;73.54 &nbsp; 0.00 &nbsp;99.04 &nbsp;67.52 &nbsp;98.49 &nbsp; &nbsp;166 &nbsp; &nbsp;0.252 &nbsp; &nbsp; 6 &nbsp; &nbsp;0.331 &nbsp; &nbsp;0.583</div>
	<div>
		&nbsp;</div>
	<div>
		&nbsp;73.54 &nbsp; 0.00 &nbsp;99.04 &nbsp;67.52 &nbsp;98.49 &nbsp; &nbsp;166 &nbsp; &nbsp;0.252 &nbsp; &nbsp; 6 &nbsp; &nbsp;0.331 &nbsp; &nbsp;0.583</div>
	<div>
		&nbsp;</div>
	<div>
		&nbsp;73.54 &nbsp; 0.00 &nbsp;99.04 &nbsp;67.52 &nbsp;98.49 &nbsp; &nbsp;166 &nbsp; &nbsp;0.252 &nbsp; &nbsp; 6 &nbsp; &nbsp;0.331 &nbsp; &nbsp;0.583</div>
</blockquote>
<div>
	&nbsp;</div>
<div>
	我们可以看到，5次young gc之后，垃圾内存被从Eden space区(E)放入了Old space区(O)，并引起了百分比的变化，导致Survivor space使用的百分比从73.54%(S0)降到0%(S1)。有效释放了内存空间。绿框中，我们可以看到，一次full gc之后，Old space区(O)的内存被回收，从99.05%降到67.52%。</div>
<div>
	&nbsp;</div>
<div>
	图中同时打印了young gc和full gc的总次数、总耗时。而，每次young gc消耗的时间，可以用相间隔的两行YGCT相减得到。每次full gc消耗的时间，可以用相隔的两行FGCT相减得到。例如红框中表示的第一行、第二行之间发生了1次young gc，消耗的时间为0.252-0.252＝0.0秒。</div>
<div>
	&nbsp;</div>
<div>
	常驻内存区(P)的使用率，始终停留在98.49%左右，说明常驻内存没有突变，比较正常。</div>
<div>
	&nbsp;</div>
<div>
	如果young gc和full gc能够正常发生，而且都能有效回收内存，常驻内存区变化不明显，则说明java内存释放情况正常，垃圾回收及时，java内存泄露的几率就会大大降低。但也不能说明一定没有内存泄露。</div>
<div>
	&nbsp;</div>
<div>
	GCT 是YGCT 和FGCT的时间总和。</div>
<div>
	&nbsp;</div>
<div>
	以上，介绍了Jstat按百分比查看gc情况的功能。其实，它还有功能，例如加载类信息统计功能、内存池信息统计功能等，那些是以绝对值的形式打印出来的，比较少用，在此就不做介绍。</div>
<blockquote>
	<div>
		[root@localhost bin]# ps -ef | grep java</div>
	<div>
		&nbsp;</div>
	<div>
		root &nbsp; &nbsp; 25917 &nbsp; &nbsp; 1 &nbsp;2 23:23 pts/2 &nbsp; &nbsp;00:00:05 /usr/local/jdk1.5/bin/java -Djava.endorsed.dirs=/usr/local/jakarta-tomcat-5.0.30/common/endorsed -classpath /usr/local/jdk1.5/lib/tools.jar:/usr/local/jakarta-tomcat-5.0.30/bin/bootstrap.jar:/usr/local/jakarta-tomcat-5.0.30/bin/commons-logging-api.jar -Dcatalina.base=/usr/local/jakarta-tomcat-5.0.30 -Dcatalina.home=/usr/local/jakarta-tomcat-5.0.30 -Djava.io.tmpdir=/usr/local/jakarta-tomcat-5.0.30/temp org.apache.catalina.startup.Bootstrap start</div>
</blockquote>
<div>
	&nbsp;</div>
<div>
	jstat -class pid:显示加载class的数量，及所占空间等信息。</div>
<div>
	&nbsp;</div>
<div>
	实例使用3：</div>
<blockquote>
	<div>
		[root@localhost bin]# jstat -class 25917</div>
	<div>
		&nbsp;</div>
	<div>
		Loaded &nbsp;Bytes &nbsp;Unloaded &nbsp;Bytes &nbsp; &nbsp; Time</div>
	<div>
		&nbsp;</div>
	<div>
		2629 &nbsp; &nbsp;2916.8 &nbsp; &nbsp; &nbsp; 29 &nbsp; 24.6 &nbsp; &nbsp; 0.90</div>
</blockquote>
<div>
	jstat -compiler pid:显示VM实时编译的数量等信息。</div>
<div>
	&nbsp;</div>
<div>
	实例使用4：</div>
<blockquote>
	<div>
		[root@localhost bin]# jstat -compiler 25917</div>
	<div>
		&nbsp;</div>
	<div>
		Compiled Failed Invalid &nbsp; Time &nbsp; FailedType FailedMethod</div>
	<div>
		&nbsp;</div>
	<div>
		&nbsp; &nbsp; &nbsp;768 &nbsp; &nbsp; &nbsp;0 &nbsp; &nbsp; &nbsp; 0 &nbsp; 0.70 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;0</div>
</blockquote>
<div>
	jstat &ndash;gccapacity :可以显示，VM内存中三代（young,old,perm）对象的使用和占用大小，如：PGCMN显示的是最小perm的内存使用量，PGCMX显示的是perm的内存最大使用量，PGC是当前新生成的perm内存占用量，PC是但前perm内存占用量。其他的可以根据这个类推， OC是old内纯的占用量。</div>
<blockquote>
	<div>
		[root@localhost bin]# jstat -gccapacity 25917</div>
	<div>
		&nbsp;</div>
	<div>
		NGCMN &nbsp; &nbsp; &nbsp; 640.0</div>
	<div>
		&nbsp;</div>
	<div>
		NGCMX &nbsp; &nbsp; &nbsp; 4992.0</div>
	<div>
		&nbsp;</div>
	<div>
		NGC &nbsp; &nbsp; &nbsp; &nbsp; 832.0</div>
	<div>
		&nbsp;</div>
	<div>
		S0C &nbsp; &nbsp; &nbsp; &nbsp; 64.0</div>
	<div>
		&nbsp;</div>
	<div>
		S1C &nbsp; &nbsp; &nbsp; &nbsp; 64.0</div>
	<div>
		&nbsp;</div>
	<div>
		EC &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;704.0</div>
	<div>
		&nbsp;</div>
	<div>
		OGCMN &nbsp; &nbsp; &nbsp; 1408.0</div>
	<div>
		&nbsp;</div>
	<div>
		OGCMX &nbsp; &nbsp; &nbsp; 60544.0</div>
	<div>
		&nbsp;</div>
	<div>
		OGC &nbsp; &nbsp; &nbsp; &nbsp; 9504.0</div>
	<div>
		&nbsp;</div>
	<div>
		OC &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;9504.0 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;OC是old内纯的占用量</div>
	<div>
		&nbsp;</div>
	<div>
		PGCMN &nbsp; &nbsp; &nbsp; 8192.0 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;PGCMN显示的是最小perm的内存使用量</div>
	<div>
		&nbsp;</div>
	<div>
		PGCMX &nbsp; &nbsp; &nbsp; 65536.0 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; PGCMX显示的是perm的内存最大使用量</div>
	<div>
		&nbsp;</div>
	<div>
		PGC &nbsp; &nbsp; &nbsp; &nbsp; 12800.0 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; PGC是当前新生成的perm内存占用量</div>
	<div>
		&nbsp;</div>
	<div>
		PC &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;12800.0 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; PC是但前perm内存占用量</div>
	<div>
		&nbsp;</div>
	<div>
		YGC &nbsp; &nbsp; &nbsp; &nbsp; 164</div>
	<div>
		&nbsp;</div>
	<div>
		FGC &nbsp; &nbsp; &nbsp; &nbsp; 6</div>
</blockquote>
<div>
	&nbsp;</div>
<div>
	jstat -gcnew pid: new对象的信息</div>
<blockquote>
	<div>
		[root@localhost bin]# jstat -gcnew 25917</div>
	<div>
		&nbsp;</div>
	<div>
		&nbsp;S0C &nbsp; &nbsp;S1C &nbsp; &nbsp;S0U &nbsp; &nbsp;S1U &nbsp; TT MTT &nbsp;DSS &nbsp; &nbsp; &nbsp;EC &nbsp; &nbsp; &nbsp; EU &nbsp; &nbsp; YGC &nbsp; &nbsp; YGCT</div>
	<div>
		&nbsp;</div>
	<div>
		&nbsp;64.0 &nbsp; 64.0 &nbsp; 47.4 &nbsp; 0.0 &nbsp; 2 &nbsp;15 &nbsp; 32.0 &nbsp; &nbsp;704.0 &nbsp; &nbsp;145.7 &nbsp; &nbsp;168 &nbsp; &nbsp;0.254</div>
</blockquote>
<div>
	&nbsp;</div>
<div>
	jstat -gcnewcapacity pid: new对象的信息及其占用量</div>
<blockquote>
	<div>
		[root@localhost bin]# jstat -gcnewcapacity 25917</div>
	<div>
		&nbsp;</div>
	<div>
		&nbsp;NGCMN &nbsp;NGCMX &nbsp; NGC &nbsp; S0CMX &nbsp;S0C &nbsp; S1CMX &nbsp;S1C &nbsp; ECMX &nbsp; &nbsp;EC &nbsp; &nbsp; &nbsp;YGC &nbsp; FGC</div>
	<div>
		&nbsp;</div>
	<div>
		640.0 &nbsp;4992.0 &nbsp;832.0 64.0 &nbsp; 448.0 448.0 &nbsp;64.0 &nbsp; 4096.0 &nbsp;704.0 &nbsp;168 &nbsp; &nbsp; 6</div>
</blockquote>
<div>
	&nbsp;</div>
<div>
	jstat -gcold pid: old对象的信息。</div>
<blockquote>
	<div>
		[root@localhost bin]# jstat -gcold 25917</div>
	<div>
		&nbsp;</div>
	<div>
		&nbsp; &nbsp;PC &nbsp; &nbsp; &nbsp; PU &nbsp; &nbsp; &nbsp; &nbsp;OC &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;OU &nbsp; &nbsp; &nbsp; YGC &nbsp; &nbsp;FGC &nbsp; &nbsp;FGCT &nbsp; &nbsp; GCT</div>
	<div>
		&nbsp;</div>
	<div>
		&nbsp;12800.0 &nbsp;12617.6 &nbsp; &nbsp; 9504.0 &nbsp; &nbsp; &nbsp;6561.3 &nbsp; 169 &nbsp; &nbsp; 6 &nbsp; &nbsp;0.335 &nbsp; &nbsp;0.591</div>
</blockquote>
<div>
	&nbsp;</div>
<div>
	jstat -gcoldcapacity pid:old对象的信息及其占用量。</div>
<blockquote>
	<div>
		[root@localhost bin]# jstat -gcoldcapacity 25917</div>
	<div>
		&nbsp;</div>
	<div>
		OGCMN &nbsp; &nbsp; &nbsp;OGCMX &nbsp; &nbsp; &nbsp; &nbsp;OGC &nbsp; &nbsp; &nbsp; &nbsp; OC &nbsp; &nbsp; &nbsp; YGC &nbsp; FGC &nbsp; &nbsp;FGCT &nbsp; &nbsp; GCT</div>
	<div>
		&nbsp;</div>
	<div>
		1408.0 &nbsp; &nbsp; 60544.0 &nbsp; &nbsp; &nbsp;9504.0 &nbsp; &nbsp; &nbsp;9504.0 &nbsp; 169 &nbsp; &nbsp; 6 &nbsp; &nbsp;0.335 &nbsp; &nbsp;0.591</div>
</blockquote>
<div>
	&nbsp;</div>
<div>
	jstat -gcpermcapacity pid: perm对象的信息及其占用量。</div>
<blockquote>
	<div>
		[root@localhost bin]# jstat -gcpermcapacity 25917</div>
	<div>
		&nbsp;</div>
	<div>
		PGCMN &nbsp; &nbsp; &nbsp;PGCMX &nbsp; &nbsp; &nbsp; PGC &nbsp; &nbsp; &nbsp; &nbsp; PC &nbsp; &nbsp; &nbsp;YGC &nbsp; FGC &nbsp; &nbsp;FGCT &nbsp; &nbsp; GCT</div>
	<div>
		&nbsp;</div>
	<div>
		8192.0 &nbsp; &nbsp;65536.0 &nbsp; &nbsp;12800.0 &nbsp; &nbsp;12800.0 &nbsp; 169 &nbsp; &nbsp; 6 &nbsp; &nbsp;0.335 &nbsp; &nbsp;0.591</div>
	<div>
		&nbsp;</div>
	<div>
		jstat -printcompilation pid:当前VM执行的信息。</div>
	<div>
		&nbsp;</div>
	<div>
		[root@localhost bin]# jstat -printcompilation -h3 &nbsp;25917 1000 5</div>
</blockquote>
<div>
	&nbsp;</div>
<div>
	每1000毫秒打印一次，一共打印5次，还可以加上-h3每三行显示一下标题。</div>
<div>
	&nbsp;</div>
<blockquote>
	<div>
		Compiled &nbsp;Size &nbsp;Type Method</div>
	<div>
		&nbsp;</div>
	<div>
		&nbsp; &nbsp; &nbsp;788 &nbsp; &nbsp; 73 &nbsp; &nbsp;1 java/io/File &lt;init&gt;</div>
	<div>
		&nbsp;</div>
	<div>
		&nbsp; &nbsp; &nbsp;788 &nbsp; &nbsp; 73 &nbsp; &nbsp;1 java/io/File &lt;init&gt;</div>
	<div>
		&nbsp;</div>
	<div>
		&nbsp; &nbsp; &nbsp;788 &nbsp; &nbsp; 73 &nbsp; &nbsp;1 java/io/File &lt;init&gt;</div>
	<div>
		&nbsp;</div>
	<div>
		Compiled &nbsp;Size &nbsp;Type Method</div>
	<div>
		&nbsp;</div>
	<div>
		&nbsp; &nbsp; &nbsp;788 &nbsp; &nbsp; 73 &nbsp; &nbsp;1 java/io/File &lt;init&gt;</div>
	<div>
		&nbsp;</div>
	<div>
		&nbsp; &nbsp; &nbsp;788 &nbsp; &nbsp; 73 &nbsp; &nbsp;1 java/io/File &lt;init&gt;</div>
</blockquote>

