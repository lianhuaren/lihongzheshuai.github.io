---
layout: post
title: slf4j+log4j2 控制台输出错误解决
date: 2013-10-17 20:12
author: onecoder
comments: true
categories: [log4j, log4j, slf4j]
---
<blockquote>
	<p>
		<span style="font-size: 12px; font-family: Monaco;">SLF4J: Failed to load class &quot;org.slf4j.impl.StaticLoggerBinder&quot;.</span><br />
		<span style="font-size: 12px; font-family: Monaco;">SLF4J: Defaulting to no-operation (NOP) logger implementation</span><br />
		<span style="font-size: 12px; font-family: Monaco;">SLF4J: See </span><span style="font-size: 12px; font-family: Monaco; color: rgb(4, 46, 238);"><u>http://www.slf4j.org/codes.html#StaticLoggerBinder</u></span><span style="font-size: 12px; font-family: Monaco;"> for further details.</span></p>
</blockquote>
<p>
	<span style="font-size: 12px; font-family: Monaco;">在使用log4j2+slf4j的时候遇到这个错误提示，这不到类。在slf4j的官网有正好有相应的说明。需要添加依赖包</span></p>
<blockquote>
	<p>
		<span style="font-size: 12px; font-family: Verdana;">slf4j-api-1.7.5.jar</span><br />
		<span style="font-size: 12px; font-family: Verdana;">slf4j-simple-1.7.5.jar</span></p>
</blockquote>
<p>
	<span style="font-size: 12px; font-family: Monaco;">在Gradle的build.gradle配置文件种添加依赖</span></p>
<pre class="brush:groovy;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
dependencies{
compile(
&quot;org.apache.logging.log4j:log4j-api:$log4j_version&quot;,
&quot;org.apache.logging.log4j:log4j-core:$log4j_version&quot;,
&quot;org.slf4j:slf4j-api:$slf4j_version&quot;,
&quot;org.slf4j:slf4j-simple:$slf4j_version&quot;
)}</pre>
<p>
	<span style="font-size: 14px; font-family: Monaco;">刷新工程。再次输出，问题解决。</span></p>

