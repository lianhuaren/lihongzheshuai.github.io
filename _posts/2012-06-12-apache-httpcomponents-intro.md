---
layout: post
title: 开源Http通信相关组件推荐：Apache HttpComponents
date: 2012-06-12 12:20
author: onecoder
comments: true
categories: [httpclient, Java, 开源, 开源项目]
---
说起Apache HttpComponents 可能大家还有些陌生，但是如果说到&nbsp;<a href="http://hc.apache.org/httpclient-legacy/index.html">Commons HttpClient</a>&nbsp;大家可能就非常熟悉了。其实Apache HttpComponents中包含的Apache HttpComponents-httpClients4.x系列，就是commons httpclient 3.x系列的升级版，也就是说，我们以后如果用的httpClient4.x以后的系列，那么都出自Apache HttpComponents了。
<div>
	PS:官方强烈推荐大家升级。(Commons HttpClient 3.x codeline is at the end of life. All users of Commons HttpClient 3.x are strongly encouraged to upgrade to HttpClient 4.1.)</div>
<div>
	<div>
		&nbsp;</div>
	<div>
		最新的httpClient版本已经为4.2GA，不过笔者在Maven库中，只能依赖到4.1版本的：</div>
	<div>
		<pre class="brush:xml;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
&lt;dependency&gt;

&lt;groupId&gt;org.apache.httpcomponents&lt;/groupId&gt;

&lt;artifactId&gt;httpclient&lt;/artifactId&gt;

&lt;version&gt;4.1&lt;/version&gt;

&lt;/dependency&gt;
</pre>
	</div>
</div>
<div>
	Apache HttpComponents &nbsp;还包含其他的一些组件，如：Apache HttpComponents &nbsp;还包含其他的一些组件，如：HttpComponents AsyncClient ，不过目前仅为4.0beta版，核心组件为：HttpComponents Core</div>
<div>
	&nbsp;</div>
<div>
	<div>
		官网地址：<span style="color: #0000ff;"><a href="http://hc.apache.org/index.html"><span style="color: #0000ff;">http://hc.apache.org/index.html</span></a></span></div>
	<div>
		下载页面：<span style="color: #0000ff;"><a href="http://hc.apache.org/downloads.cgi"><span style="color: #0000ff;">http://hc.apache.org/downloads.cgi</span></a></span></div>
	<div>
		SVN下载地址：svn checkout&nbsp;http://svn.apache.org/repos/asf/httpcomponents/project-trunk</div>
</div>

