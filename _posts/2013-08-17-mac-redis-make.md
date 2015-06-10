---
layout: post
title: Mac OS下 Redis2.6.14部署记录
date: 2013-08-17 15:16
author: onecoder
comments: true
categories: [mac, make, os, Redis, Redis]
---
<p>
	<span style="font-size: 14px; font-family: Arial;">部署一个Redis作为缓存进行验证，记录部署过程。</span><br />
	<span style="font-size: 14px; font-family: Arial;">官网：</span><span style="font-size: 14px; font-family: Arial; color: rgb(4, 46, 238);"><u>http://redis.io/</u></span><span style="font-size: 14px; font-family: Arial;">，目前最近稳定版为2.6.14</span><br />
	<span style="font-size: 14px; font-family: Arial;">&nbsp;解压，进入目录。按照README文件的指引进行编译和验证。</span><br />
	<span style="font-size: 14px; font-family: Arial;">在解压后的根目录执行</span></p>
<pre class="brush:shell;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
$&gt;make
</pre>
<p style="text-align: center;">
	<img alt="" src="http://onecoder.qiniudn.com/8wuliao/D5NwfLYI/4YJxH.jpg" style="width: 630px; height: 629px;" /></p>
<p>
	<span style="font-size: 14px; font-family: Arial;">执行后，可以通过</span></p>
<pre class="brush:shell;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
$&gt;make test
</pre>
<p>
	<span style="font-size: 14px; font-family: Arial;">进行验证,基本看到的就是一堆OK。</span></p>
<p style="text-align: center;">
	<img alt="" src="http://onecoder.qiniudn.com/8wuliao/D5NwgrjH/F79po.jpg" style="width: 630px; height: 631px;" /></p>
<p>
	<span style="font-size: 14px; font-family: Arial;">编译完成，启动Redis服务。进入src目录。</span></p>
<pre class="brush:shell;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
$&gt;cd src
$&gt;./redis-server
</pre>
<p style="text-align: center;">
	<img alt="" src="http://onecoder.qiniudn.com/8wuliao/D5NwgQjv/6UFY8.jpg" style="width: 630px; height: 629px;" /></p>
<p>
	<span style="font-size: 14px; font-family: Arial;">至此，redis服务就启动好了，如果你想把redis相关的命令安装到/usr/local/bin下，</span><br />
	<span style="font-size: 14px; font-family: Arial;">可以执行</span></p>
<pre class="brush:shell;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
$&gt;make install
</pre>
<p style="text-align: center;">
	<img alt="" src="http://onecoder.qiniudn.com/8wuliao/D5NvW0mD/145AJr.jpg" style="width: 630px; height: 631px;" /></p>
<p>
	<span style="font-size: 14px; font-family: Arial;">启动后，可以通过客户端命令验证服务是否正常运行。</span></p>
<p style="text-align: center;">
	<img alt="" src="http://onecoder.qiniudn.com/8wuliao/D5NwgS1v/fuz6y.jpg" style="width: 630px; height: 245px;" /></p>
<p>
	<span style="font-size: 14px; font-family: Arial;">至此，一个redis服务就部署完成了，如果想通过Java访问可以下载Jedis包，API非常简单。 &nbsp; &nbsp;&nbsp;</span></p>

