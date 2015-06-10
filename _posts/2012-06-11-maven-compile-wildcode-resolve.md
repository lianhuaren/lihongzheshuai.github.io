---
layout: post
title: Maven编译报错乱码问题和编译问题解决
date: 2012-06-11 20:07
author: onecoder
comments: true
categories: [Maven, Maven, 乱码, 开发环境, 编译]
---
最近需要用Maven打包工程，却不想遇到乱码问题。
<div>
	&nbsp;</div>
<div>
	问题：</div>
<div>
	&nbsp;</div>
<div>
	在Eclipse中通过Maven Plugin执行install 命令报错如下：</div>
<div>
	&nbsp;</div>
<div>
	[ERROR] Failure executing javac, &nbsp;but could not parse the error:</div>
&ldquo;一串乱码&rdquo;
<div>
	&nbsp;</div>
<div>
	错误信息都是乱码，问题解决起来就头疼了。所以决定先解决乱码问题。经过一番搜索排查终于找到了办法：</div>
<div>
	在控制面板的，区域和语言中，将非Unicode语言改为英语美国即可。</div>
<div>
	<a href="http://www.coderli.com/wp-content/uploads/2012/06/maven-local.jpg"><img alt="区域设置" height="436" src="http://www.coderli.com/wp-content/uploads/2012/06/maven-local.jpg" title="maven-local" width="464" /></a></div>
<div>
	&nbsp;</div>
<div>
	(注：笔者系统为win7，xp听说没这个选项？如果没有，改位置的里的信息试试。同理，如果你已经是英语了但是还乱码，那就改成中文的，总之取决于你的Maven环境和你的系统语言的匹配。)
	<div>
		&nbsp;</div>
	<div>
		改后重启，再编译，错误信息出来了：</div>
	<div>
		&nbsp;</div>
	<div>
		<div>
			<span style="color: #ff0000;">[ERROR] Failure executing javac, &nbsp;but could not parse the error:</span></div>
		<span style="color: #ff0000;">The system cannot find the path specifie.</span></div>
	<div>
		&nbsp;</div>
	<div>
		第一反映就是检查path里配置，用：</div>
	<div>
		echo %path%</div>
	打印path里的结果，没什么问题。
	<div>
		&nbsp;</div>
	<div>
		猛然间，笔者想起，我们的Maven工程里，自定义了一个变量，用于工程编译的：</div>
	<div>
		<pre class="brush:xml;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
 org.apache.maven.plugins maven-compiler-plugin 2.3.2 true true ${JAVA_1_6_HOME}/bin/javac 1.6 1.6 1.6 
</pre>
	</div>
	<div>
		这是强制大家用1.6版本JDK进行工程编译。这个变量是在各自Maven setting.xml文件中赋值的。</div>
	<div>
		<pre class="brush:xml;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
 compiler C:/Program Files/Java/jdk1.6.0_30 
</pre>
	</div>
	<div>
		问题就在这，笔者最近升级了1.6版本的jdk到32，而这里还配置的30的路径，自己找不到了。将这里的值改为正确的路径。再次编译，成功！</div>
</div>

