---
layout: post
title: Mac下 Hbase部署简介(Mac OSX 10.8.3 + HBase-0.94.6)
date: 2013-04-07 23:07
author: onecoder
comments: true
categories: [hadoop, Hadoop, hbase, mac]
---
<p>
	1、安装JDK。之前在部署Hadoop的时候已经安装完成。<br />
	2、下载解压HBase。<br />
	3、配置HBase数据存储路径，虽然单机模式可以使用本地文件系统，不过OneCoder还是配置HDFS文件系统。<br />
	修改hbase-site.xml</p>
<pre class="brush:xml;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
&lt;configuration&gt;
    &lt;property&gt;
        &lt;name&gt;hbase.rootdir&lt;/name&gt;
        &lt;value&gt;hdfs://localhost:40000/hbase&lt;/value&gt;
    &lt;/property&gt;
    &lt;property&gt;
        &lt;name&gt;hbase.cluster.distributed&lt;/name&gt;
        &lt;value&gt;true&lt;/value&gt;
    &lt;/property&gt;
&lt;/configuration&gt;
</pre>
<p>
	这里hdfs路径跟之前hadoop环境中core-site.xml的配置保持一直。<br />
	4、修改conf/hbase-site.xml。配置JAVA_HOME<br />
	5、启动HBase</p>
<pre class="brush:shell;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
shell&gt;bin/start-hbase.sh</pre>
<p>
	6、验证安装</p>
<pre class="brush:shell;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
shell&gt;bin/hbase shell
hbase&gt;status</pre>
<blockquote>
	<p>
		hbase(main):001:0&gt; status</p>
	<p>
		2013-04-07 22:58:24.203 java[3826:1703] Unable to load realm info from SCDynamicStore<br />
		1 servers, 0 dead, 2.0000 average load</p>
</blockquote>
<p>
	部署成功。这里还是存在java的警告。同样配置了-Djava.security.krb5.realm=OX.AC.UK -Djava.security.krb5.kdc=kdc0.ox.ac.uk:kdc1.ox.ac.uk 仍然无法消除。暂且如此。</p>

