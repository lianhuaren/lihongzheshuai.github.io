---
layout: post
title: Cloudera CDH4卸载
date: 2013-04-27 21:09
author: onecoder
comments: true
categories: [cdh4, cloudera, Hadoop, uninstall]
---
<p>
	记录卸载过程和问题。现有环境Cloudera Manager + (1 + 2 )的CDH环境。</p>
<p>
	1、先在Manage管理端移除所有服务。<br />
	2、删除Manager Server<br />
	在Manager节点运行</p>
<pre class="brush:shell;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
$ sudo /usr/share/cmf/uninstall-cloudera-manager.sh
</pre>
<p>
	如果没有该脚本，则可以手动删除，先停止服务：</p>
<pre class="brush:shell;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
sudo service cloudera-scm-server stop
sudo service cloudera-scm-server-db stop
</pre>
<p>
	然后删除：</p>
<pre class="brush:shell;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
sudo yum remove cloudera-manager-server
sudo yum remove cloudera-manager-server-db
</pre>
<p>
	3、删除所有CDH节点上的CDH服务，先停止服务：</p>
<pre class="brush:shell;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
sudo service cloudera-scm-agent hard_stop
</pre>
<p>
	卸载安装的软件：</p>
<pre class="brush:shell;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
sudo yum remove &#39;cloudera-manager-*&#39; hadoop hue-common &#39;bigtop-*&#39;
</pre>
<p>
	4、删除残余数据：</p>
<pre class="brush:shell;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
sudo rm -Rf /usr/share/cmf /var/lib/cloudera* /var/cache/yum/cloudera*
</pre>
<p>
	5、kill掉所有Manager和Hadoop进程（选作，如果你正确停止Cloud Manager和所有服务则无须此步）</p>
<pre class="brush:shell;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
$ for u in hdfs mapred cloudera-scm hbase hue zookeeper oozie hive impala flume; do sudo kill $(ps -u $u -o pid=); done
</pre>
<p>
	6、删除Manager的lock文件<br />
	在Manager节点运行：</p>
<pre class="brush:shell;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
sudo rm /tmp/.scm_prepare_node.lock
</pre>
<p>
	至此，删除完成。<br />
	&nbsp;</p>
<p>
	&nbsp;</p>

