---
layout: post
title: 教你打包Java程序，jar转exe随处可跑
date: 2012-06-16 11:51
author: onecoder
comments: true
categories: [Eclipse, exe, jar, Java, Java基础]
---
发现很多人问如何把Jar转成exe程序。可能是想双击运行和随处运行。其实这个并不难，我就简单总结几种方法，供大家参考，关键还是要知其所以然。
<div>
	&nbsp;</div>
<div>
	Java程序的运行不可能脱离JRE，不管你是Jar包还是exe程序。这点你必须了解。那么在没有JRE的机器上你的程序怎么跑？很简单，在你程序里带一份JRE就行了。</div>
<div>
	&nbsp;</div>
<div>
	先介绍集中打包的方法：</div>
<div>
	&nbsp;</div>
<div>
	<div>
		<strong>Eclipse：</strong></div>
	<div>
		&nbsp;</div>
	<div>
		最简单直接的方法，选择你想打包的程序，右键export...</div>
</div>
<div>
	&nbsp;</div>
<div>
	<a href="http://www.coderli.com/wp-content/uploads/2012/06/export.jpg"><img alt="export" class="alignnone  wp-image-501" height="300" src="http://www.coderli.com/wp-content/uploads/2012/06/export.jpg" title="export" width="619" /></a></div>
<div>
	&nbsp;</div>
<div>
	<div>
		选择Runnable Jar file。（即可执行的Jar包）</div>
</div>
<div>
	&nbsp;</div>
<div>
	<a href="http://www.coderli.com/wp-content/uploads/2012/06/runablejar.jpg"><img alt="runablejar" class="alignnone  wp-image-504" height="300" src="http://www.coderli.com/wp-content/uploads/2012/06/runablejar.jpg" title="runablejar" width="621" /></a></div>
<div>
	&nbsp;</div>
<div>
	<div>
		选择你程序的主类，就是还有Main函数的类。点Finish即可。</div>
	<div>
		&nbsp;</div>
	<div>
		在你的机器上，设置好Jar文件的打开方式（别默认用解压缩的工具打开就行），双击即可运行。</div>
</div>
<div>
	&nbsp;</div>
<div>
	<a href="http://www.coderli.com/wp-content/uploads/2012/06/forfunjar.jpg"><img alt="forfunjar" class="alignnone size-full wp-image-503" height="316" src="http://www.coderli.com/wp-content/uploads/2012/06/forfunjar.jpg" title="forfunjar" width="308" /></a></div>
<div>
	&nbsp;</div>
<div>
	<div>
		这个跟在命令行执行：</div>
	<div>
		&nbsp;</div>
	<pre>
java -jar forfun.jar</pre>
	的效果是一样的。
	<div>
		&nbsp;</div>
	其实一个Jar能运行，关键还是配置Jar内部的MANIFEST.MF文件。该文件存在于Jar包根目录的META-INF文件夹内。主要由于指定主类(Main)的位置：
	<div>
		&nbsp;</div>
	<div>
		<div>
			<span style="color: #ff0000;">Manifest-Version: 1.0</span></div>
		<span style="color: #ff0000;">Main-Class: one.coder.jdk.JDKMain</span></div>
	<div>
		&nbsp;</div>
	<div>
		版本可以自己指定，默认生成是1.0。主类位置需要指定。注意，<span style="color: #ff0000;">Main-Class的冒号后，要跟一个空格。</span></div>
	如果你还有要依赖的Jar包，则可以配置Class-Path来指定。
	<div>
		&nbsp;</div>
	<div>
		<pre>
<span style="color: #ff0000;">Class-Path:&nbsp;./ logback-core-0.9.29.jar junit-4.9.jar slf4j-api-1.</span>
<span style="color: #ff0000;">&nbsp;6.1.jar logback-classic-0.9.29.jar hamcrest-core-1.1.jar</span></pre>
		<div>
			&nbsp;</div>
		打开刚才生成的Jar文件，你可能发现多了一些Eclipse的东西，并且主类变成了</div>
	<div>
		&nbsp;</div>
	<span style="color: #ff0000;">Main-Class: org.eclipse.jdt.internal.jarinjarloader.JarRsrcLoader</span>
	<div>
		&nbsp;</div>
	<div>
		也就是通过Eclipse提供的这个主类来加载的你程序。如果你不喜欢这样，将其去掉。自己进行配置。笔者通过一个不依赖任何Jar包的小程序进行说明。</div>
</div>
<div>
	&nbsp;</div>
<div>
	<a href="http://www.coderli.com/wp-content/uploads/2012/06/runjar.jpg"><img alt="" class="alignnone  wp-image-505" height="300" src="http://www.coderli.com/wp-content/uploads/2012/06/runjar.jpg" title="runjar" width="600" /></a></div>
<div>
	&nbsp;</div>
<div>
	这是笔者打出Jar的内部截图，去掉所有跟我的程序不相关的东西。MANIFEST.MF的配置也很简单。
	<div>
		&nbsp;</div>
	<div>
		<div>
			<span style="color: #ff0000;">Manifest-Version: 1.0</span></div>
		<span style="color: #ff0000;">Main-Class: one.coder.jdk.JDKMain</span></div>
	<div>
		&nbsp;</div>
	在有JRE的机器上，双击一样可以运行。
	<div>
		&nbsp;</div>
	<span style="color: #ff0000;">注：这里需要提一下，尽量不用用解压软件自带的编辑器进行编辑，如果你编辑后发现不能运行，提示打开jar错误等信息，很可能是由于你编辑的MANIFEST.MF文件的编码错误。导致无法解析。默认是采用ANSI编码格式的。不要改成UTF-8等</span>。笔者被这个问题，困扰了近半个小时。
	<div>
		&nbsp;</div>
	接下来说说在没有JRE的机器上怎么办？
	<div>
		&nbsp;</div>
	最简单的手动的办法就是写一个bat脚本。并且带一份jre在你的程序里。</div>
<div>
	<a href="http://www.coderli.com/wp-content/uploads/2012/06/files.jpg"><img alt="files" class="alignnone size-full wp-image-502" height="123" src="http://www.coderli.com/wp-content/uploads/2012/06/files.jpg" title="files" width="273" /></a></div>
<div>
	&nbsp;</div>
<div>
	<div>
		把图中的三个文件，放入同一个文件夹中。</div>
	start.bat内容如下:
	<div>
		&nbsp;</div>
	<div>
		<span style="color: #ff0000;">.\jre7\bin\java -jar .\run.jar</span></div>
	<div>
		&nbsp;</div>
	说白了就似乎调用jre中的java命令，执行指定的jar程序。 双击start.bat，执行成功。
	<div>
		&nbsp;</div>
	<div>
		如果你非要打成exe程序，笔者推荐一个工具JSmooth。简单好用。同样这也肯定是需要JRE指定的。</div>
	关于JSmooth的教程，笔者找到了一个不错的教学贴：<span style="color: #0000ff;"><a href="http://yisufuyou.iteye.com/blog/403556"><span style="color: #0000ff;">http://yisufuyou.iteye.com/blog/403556</span></a></span>
	<div>
		&nbsp;</div>
	<div>
		按照里面的步骤，你一定可以成功。</div>
	<div>
		&nbsp;</div>
	<div>
		<a href="http://www.coderli.com/wp-content/uploads/2012/06/coderexe.jpg"><img alt="" class="alignnone size-full wp-image-500" height="123" src="http://www.coderli.com/wp-content/uploads/2012/06/coderexe.jpg" title="coderexe" width="189" /></a></div>
</div>
<div>
	&nbsp;</div>
<div>
	说了这么多，如果还有什么不明白的，可以给我留言，一起讨论研究。 PS1:默认的JRE体积实在太大，你可以考虑精简JRE还节约空间，这部分内容，不在本文讨论。另外，如果你想把你的软件做成安装包的形势，可考虑InstallAnyWhere（<a href="http://www.flexerasoftware.com/products/installanywhere.htm">http://www.flexerasoftware.com/products/installanywhere.htm</a>）这个工具，会有
	<div>
		&nbsp;</div>
	<div>
		PS2：笔者研究的过程中，为了模拟没有JRE的环境，真是百般折腾，因为笔者把所有环境变量都删掉，还有可以运行。不知道是不是从JDK7开始，Java居然在我的System32路径下，也放置Java.exe等程序，也就是说，不用配置Path了。jre路径的指定，貌似也写入了注册表，不过这点，笔者没有亲自证实，只是在注册表中简单的搜索了一下，仅发现了JavaFX的配置和一些其他的Java配置，没有深入研究，不好定论。有兴趣可以研究下，也麻烦告诉我一声。</div>
	<div>
		&nbsp;</div>
</div>

