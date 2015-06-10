---
layout: post
title: Eclipse小技巧 增加JVM参数显示状态条
date: 2012-08-01 21:15
author: onecoder
comments: true
categories: [Eclipse, eclipse.jvm, 状态栏]
---
<p>
	动手来为你的Eclipse增加一个能显示当前Eclipse使用的JVM配置的工具条吧。简单的很。</p>
<p style="text-align: center; ">
	<img alt="bar" src="http://onecoder.qiniudn.com/8wuliao/C9QumDdk/uVp7a.jpg" style="width: 247px; height: 66px; " /></p>
<ul>
	<li>
		在eclipse根目录下建立一个文件，文件名options,不要加后缀直接保存，文件内容</li>
</ul>
<blockquote>
	<p>
		org.eclipse.ui/perf/showHeapStatus=true</p>
</blockquote>
<p style="text-align: center; ">
	<img alt="" src="http://onecoder.qiniudn.com/8wuliao/C9Qun4SJ/ZlXDP.jpg" /></p>
<ul>
	<li>
		修改eclipse目录下的eclipse.ini文件，在文件起始部分添加如下内容：</li>
</ul>
<blockquote>
	<p>
		-debug&nbsp;<br />
		options&nbsp;<br />
		-vm&nbsp;<br />
		javaw.exe</p>
</blockquote>
<p style="text-align: center; ">
	<img alt="" src="http://onecoder.qiniudn.com/8wuliao/C9QumOB6/x4STk.jpg" /></p>
<p>
	重启即可。</p>

