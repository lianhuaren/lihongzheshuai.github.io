---
layout: post
title: Gradle 使用Maven本地缓存库
date: 2013-10-08 21:20
author: onecoder
comments: true
categories: [Gradle, gradle, local, Maven]
---
<p>
	<span style="font-size: 13px; font-family: 'Microsoft YaHei';">从Maven切换到Gradle，原有的几G的本地缓存库当然想继续使用。在用户手册中找到了答案。在50.6.3章节。</span><br />
	<br />
	<span style="font-size: 13px; font-family: 'Microsoft YaHei';">如果想使用Maven本地缓存，需要定义：</span></p>
<pre class="brush:groovy;title:'build.gradle';first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
repositories {
    mavenLocal()
}
</pre>
<p>
	<span style="font-size: 13px; font-family: 'Microsoft YaHei';">Gradle使用与Maven相同的策略去定位本地Maven缓存的位置。如果在settings.xml中定义了本地Maven仓库的地址，则使用该地址。在USER_HOME/.m2下的settings.xml文件中的配置会覆盖存放在M2_HOME/conf下的settings.xml文件中的配置。如果没有settings.xml配置文件，Gradle会使用默认的USER_HOME/.m2/repository地址。</span></p>

