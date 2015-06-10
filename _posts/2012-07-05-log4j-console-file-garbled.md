---
layout: post
title: log4j 控制台和文件输出乱码问题解决
date: 2012-07-05 23:10
author: onecoder
comments: true
categories: [log4j, log4j, 乱码]
---
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	一个小问题，却让我感觉到，现在真正动脑的人很少。。我来说说吧。</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	&nbsp;</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	今天遇到一个小问题，log4j输出到文件乱码，控制台正常。显然是编码问题导致。Google一搜，几乎一水的说：<br />
	<br />
	<br />
	<br />
	<br />
	<br />
	&nbsp;</div>
<blockquote>
	<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
		<span style="text-align: -webkit-auto; color: rgb(102, 102, 102); line-height: 21px; ">项目中log4j在英文版linux下输出中文日志为乱码。</span><br style="text-align: -webkit-auto; line-height: 21px; color: rgb(102, 102, 102);  " />
		<span style="text-align: -webkit-auto; color: rgb(102, 102, 102); line-height: 21px; ">由于log4j配置文件中没有设置编码格式(encoding)，所以log4j就使用系统默认编码。导致乱码。</span><br style="text-align: -webkit-auto; line-height: 21px; color: rgb(102, 102, 102); " />
		<span style="text-align: -webkit-auto; color: rgb(102, 102, 102); line-height: 21px; ">解决方法是设置编码格式UTF-8,方法为：</span><br style="text-align: -webkit-auto; line-height: 21px; color: rgb(102, 102, 102); " />
		<span style="text-align: -webkit-auto; color: rgb(102, 102, 102); line-height: 21px; ">log4j.appender.syslog.encoding=UTF-8</span></div>
</blockquote>
<span style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">这显然是转的，因为全网几乎一样。</span><span style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">这是properties配置的，还不是xml的。如果要xml的，配置如下：</span><br />
<pre class="brush:xml;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
&lt;appender name=&quot;A1&quot; class=&quot;org.apache.log4j.RollingFileAppender&quot;&gt;
        &lt;param name=&quot;Encoding&quot; value=&quot;UTF-8&quot; /&gt;
        &lt;param name=&quot;File&quot; value=&quot;all.log&quot; /&gt;
        ......
&lt;/appender&gt;
</pre>
<br />
<span style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">但是，我是已经设置成UTF-8，而乱码了。所以，上述答案是不严谨的。</span><br />
<br />
<span style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">先说说笔者的情况吧，其实笔者的问题很简单，两套log4j appender配置，一个输出的文件，一个控制台，文件的配置了utf-8编码，控制台没配置。现象，控制台正常，文件乱码。</span>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	&nbsp;</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	把文件的改成gbk，不乱了。控制台改成gbk，乱码。控制台改成utf-8，正常。到这里你可能糊涂了。怎么这么乱？</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	&nbsp;</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	其实道理很简单，乱码，自然是编码不匹配。什么匹配？log4j用utf-8输入，你文件是不是utf-8接受的呢？检查一下，果然不是，改成utf-8编码，解决。</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	&nbsp;</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	你可能要问了，那控制台的匹配在哪里？Eclipse控制台也有是编码的，我想你应该知道。Eclipse控制台是有编码，而且，不仅仅是有，你还可以为每个执行的程序，设置独立的编码。<br />
	<br />
	<img alt="" src="http://www.coderli.com/wp-content/uploads/2012/07/eclipse-code.png" style="width: 600px; height: 418px; " /><br />
	<br />
	<span style="text-align: -webkit-auto; ">自然，这里的编码匹配了，也就不会乱码了。</span></div>

