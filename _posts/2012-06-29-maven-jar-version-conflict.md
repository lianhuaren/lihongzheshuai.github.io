---
layout: post
title: Maven 解决Jar包版本冲突
date: 2012-06-29 21:42
author: onecoder
comments: true
categories: [jar, Maven, Maven, 冲突]
---
<span style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">今天遇到一个小问题。在使用apache-commons-codec包进行编码/解码的时候，用到了Base64类的decodeBase64(String base64String)方法，这个方法在1.4版中才提供。而我们的工程中对codec存在两个间接的依赖，一个依赖1.2版本，一个依赖1.4版本，打包后，只有1.2版本，所以会报找不到该方法的错误。</span>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	&nbsp;</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	这个问题其实很好解决。在依赖1.2版本的项目上，排除对codec的间接依赖即可。如：<br />
	<br />
	<pre class="brush:xml;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&lt;dependency&gt;
                        &lt;!-- 你直接依赖的Jar包--&gt;
			&lt;groupId&gt;AAA&lt;/groupId&gt;
			&lt;artifactId&gt;AAA&lt;/artifactId&gt;
			&lt;exclusions&gt;
				&lt;exclusion&gt;
                                        &lt;!-- 你想排除的间接依赖的Jar包--&gt;
					&lt;artifactId&gt;xxx&lt;/artifactId&gt;
					&lt;groupId&gt;xxx&lt;/groupId&gt;
				&lt;/exclusion&gt;
			&lt;/exclusions&gt;
			&lt;version&gt;AAA&lt;/version&gt;
		&lt;/dependency&gt;
</pre>
</div>

