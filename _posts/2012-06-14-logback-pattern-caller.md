---
layout: post
title: Logback控制台输出类名行号带链接的Pattern配置
date: 2012-06-14 23:28
author: onecoder
comments: true
categories: [caller, Java, logback, logback, pattern]
---
<p>
	从Log4j切换到logback会发现，原来在log4j使用的日志格式：<span style="color: #ff0000;">%l&nbsp;&nbsp;</span>的功能不见了<span style="color: #ff0000;">。</span>Eclipse控制台的输出，不再带有可快速进入的链接了。</p>
<div>
	&nbsp;在logback里，需要使用%c%L才能打印出完整的类路径和行号。但是却没有链接。查阅了一下，发现了caller这个Pattern</div>
<div>
	配置好caller：%caller{1}后，链接终于又出现了。效果如下：</div>
<div>
	&nbsp;</div>
<div>
	<a href="http://www.coderli.com/wp-content/uploads/2012/06/logback-console1.jpg"><img alt="logback-console" class="alignnone  wp-image-478" height="72" src="http://www.coderli.com/wp-content/uploads/2012/06/logback-console1.jpg" title="logback-console" width="600" /></a></div>
<div>
	&nbsp;</div>
<div>
	<div>
		虽然感觉上，没原来的好看了，不过好歹，这个功能是有了。如果你想去掉烦人的Caller+0字样，还可以继续使用replace进行替换。</div>
	<div>
		&nbsp;</div>
	<div>
		附上笔者使用的logback pattern配置：</div>
	<pre class="brush:xml;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
%d{yyyy/MM/dd-HH:mm:ss} %level [%thread] %caller{1} - %msg%n
</pre>
	<div>
		关于logback pattern 转换符的说明，笔者找到了这个帖子，说的还是比较详细的：</div>
	<div>
		<span style="color: #0000ff;"><a href="http://aub.iteye.com/blog/1103685"><span style="color: #0000ff;">http://aub.iteye.com/blog/1103685</span></a></span></div>
</div>

