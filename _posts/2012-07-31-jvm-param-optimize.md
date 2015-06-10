---
layout: post
title: JVM参数说明 调优
date: 2012-07-31 23:49
author: onecoder
comments: true
categories: [Java进阶, jvm, 参数, 调优]
---
<p>
	JAVA世界中的大多数应用系统都是运行在J2EE应用服务器上的，而J2EE应用服务器又是运行在JVM上的，生成环境中JVM参数的优化和设置对于J2EE应用系统性能有着决定性的作用。要优化系统，则需要对JVM参数进行合理的设置，所以我们需要了解究竟在什么地方进行设置、有哪些参数以及各参数的意义分别是什么，并且我们还得了解JVM的内存管理机制究竟是个什么玩意儿？其实我们在网上搜索引擎上，一搜就有可以获取到一大把相关信息，关键是我们如何深入的理解它们。<br />
	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; JVM的早期版本并没有进行分区管理；这样的后果是JVM进行垃圾回收时，不得不扫描JVM所管理的整片内存，所以搜集垃圾是很耗费资源的事情，也是早起JAVA程序的性能低下的主要原因。随着JVM的发展，JVM引进了分区管理的机制。JVM所管理的所有内存资源分为2个大的部分。永久存储区（Permanent Space） 和堆空间（The Heap Space）。其中堆空间又分为养老区和新生区，新生区又分为伊甸园，幸存者0区、幸存1区。如下图：</p>
<p style="text-align: center; ">
	<img alt="" src="http://onecoder.qiniudn.com/8wuliao/C9Kev0vN/C1QwK.jpg" style="width: 624px; height: 333px; " /></p>
<p style="text-align: left; ">
	下面以Tomcat服务器为例来分析下内存溢出问题，在%TOMCAT_HOME%/bin/catalina.bat文件中加入<br />
	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; set JAVA_HOME=C:\java\jdk1.6.0_07<br />
	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; set JAVA_OPTS=%JAVA_OPTS% -Xms512m-Xmx512m -Xss1024K -XX:PermSize=256m -XX:MaxPermSize=256m<br />
	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 参数说明：<br />
	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <strong>-server</strong>启用能够执行优化的编译器, 显著提高服务器的性能，但使用能够执行优化的编译器时，服务器的预备时间将会较长。生产环境的服务器强烈推荐设置此参数。<br />
	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <strong>-Xss</strong>单个线程堆栈大小值；JDK5.0 以后每个线程堆栈大小为1M，以前每个线程堆栈大小为256K。在相同物理内存下，减小这个值能生成更多的线程。但是操作系统对一个进程内的线程数还是有限制的，不能无限生成，经验值在3000~5000左右。<br />
	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <strong>-XX:+UseParNewGC</strong>可用来设置年轻代为并发收集【多CPU】，如果你的服务器有多个CPU，你可以开启此参数；开启此参数，多个CPU 可并发进行垃圾回收，可提高垃圾回收的速度。此参数和+UseParallelGC，-XX:ParallelGCThreads搭配使用。<br />
	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <strong>+UseParallelGC</strong>选择垃圾收集器为并行收集器。此配置仅对年轻代有效。即上述配置下，年轻代使用并发收集，而年老代仍旧使用串行收集。可提高系统的吞吐量。<br />
	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <strong>-XX:ParallelGCThreads</strong>年轻代并行垃圾收集的前提下（对并发也有效果）的线程数，增加并行度，即：同时多少个线程一起进行垃圾回收。此值最好配置与处理器数目相等。永久存储区相关参数：参数名参数说明<br />
	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <strong>-Xnoclassgc</strong>每次永久存储区满了后一般GC 算法在做扩展分配内存前都会触发一次FULL GC，除非设置了-Xnoclassgc.<br />
	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <strong>-XX:PermSize</strong>应用服务器启动时，永久存储区的初始内存大<br />
	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <strong>-XX:MaxPermSize</strong>应用运行中，永久存储区的极限值。为了不消耗扩大JVM 永久存储区分配的开销，将此参数和-XX:PermSize这个两个值设为相等。堆空间相关参数参数名参数说明<br />
	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <strong>-Xms</strong>启动应用时，JVM 堆空间的初始大小值。<br />
	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <strong>-Xmx</strong>应用运行中，JVM 堆空间的极限值。为了不消耗扩大JVM 堆控件分配的开销，将此参数和-Xms 这个两个值设为相等，考虑到需要开线程，讲此值设置为总内存的80%.<br />
	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <strong>-Xmn</strong>此参数硬性规定堆空间的新生代空间大小，推荐设为堆空间大小的1/4。<br />
	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 上面所列的JVM 参数关系到系统的性能，而其中-XX:PermSize，-XX:MaxPermSize，-Xms，-Xmx 和-Xmn 这5 个参数更是直接关系到系统的性能，系统是否会出现内存溢出。<br />
	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <strong>-XX:PermSize</strong> 和-XX:MaxPermSize 分别设置应用服务器启动时，永久存储区的初始大小和极限大小；在生成环境中强烈推荐将这个两个值设置为相同的值，以避免分配永久存储区的开销，具体的值可取系统&ldquo;疲劳测试&rdquo;获取到的永久存储区的极限值；如果不进行设置-XX:MaxPermSize 默认值为64M,一般来说系统的类定义文件大小都会超过这个默认值。<br />
	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <strong>-Xms</strong> 和<strong>-Xmx</strong> 分别是服务器启动时，堆空间的初始大小和极限值。-Xms的默认值是物理内存的1/64 但小于1G，-Xmx 的默认值是物理内存的1/4 但小于1G.在生产环境中这些默认值是肯定不能满足我们的需要的。也就是你的服务器有8g 的内存，不对JVM 参数进行设置优化，应用服务器启动时还是按默认值来分配和约束JVM 对内存资源的使用，不会充分的利用所有的内存资源。<br />
	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 结论：&ldquo;永久存储区溢出（java.lang.OutOfMemoryError:Java Permanent Space）&rdquo;乃是永久存储区设置太小，不能满足系统需要的大小，此时只需要调整-XX:PermSize 和-XX:MaxPermSize 这两个参数即可。&ldquo;JVM 堆空间溢出（java.lang.OutOfMemoryError: Java heap space）&rdquo;错误是JVM 堆空间不足，此时只需要调整-Xms 和-Xmx 这两个参数即可。<br />
	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 到此我们知道了，当系统出现内存溢出时，是哪些参数设置不合理需要调整。但我们怎么知道服务器启动时，到底JVM 内存相关参数的值是多少呢？<br />
	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 这个问题其实Sun公司早已经意料到了，所以给我们开发了内存使用监控工具jvmstat，可以到ORACLE官网进行下载。用它可以很方便的看到我们的服务器内存使用情况。<br />
	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 将下载的jvmstat包解压到如&ldquo;C:\ProgramFiles\Java\jvmstat&rdquo;（这是我本地java路径，大家可以根据自己所安装的java环境的路径进行解压）。启动完之后我们就可以使用visualgc命令了，cmd进入命令符窗口，输入tasklist（windows下查看进程任务PID）查找到你要检测进程PID.然后直接输入visuglgc PID 就会弹出三个可见视图：</p>
<p style="text-align: left; ">
	<img alt="" src="http://onecoder.qiniudn.com/8wuliao/C9Ki67Wp/R4GXg.jpg" style="height: 461px; width: 600px; " /></p>
<p style="text-align: left; ">
	转自：<a href="http://hi.baidu.com/jadmin/item/39be68d3d93b39e4b3f7770e">http://hi.baidu.com/jadmin/item/39be68d3d93b39e4b3f7770e</a></p>

