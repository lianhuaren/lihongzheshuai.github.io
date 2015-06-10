---
layout: post
title: 精简代码，节省数据库空间
date: 2012-07-20 19:54
author: onecoder
comments: true
categories: [空间, 站点运营]
---
<p>
	头脑发热的折腾了两天VPS，在老婆的警告和自己的惊醒下，告一段落。我有时也会鄙视自己的脑残想法和那种&ldquo;强迫&rdquo;的心里。</p>
<p>
	从头描述一下吧。弄个人博客，网站空间，流量都是要钱的。关键的是，一旦达到了限制，网站可能就是临时无法运行。像博客、论坛这种东西，都是动态的内容，耗费的是数据库的空间。而笔者的空间恰恰少的就是数据库。只有100M。所以，笔者就开始担心起空间的容量问题。笔者现在的博客+论坛一共用了14M左右。论坛是相当的吃空间啊。</p>
<p>
	于是开始寻觅国外空间(所谓不限空间，不限流量)和VPS，其实国外空间虽然不限空间和流量，但是一般会显示CPU使用率和文件数量等。也就是限制的是你的PV。也就等于限制了流量。不过数据库方面，确实似乎没什么问题了。VPS，自然没什么问题，问题就是贵。</p>
<p>
	说了半天，和精简代码什么关系呢？</p>
<p>
	在折腾的过程中，受到老婆深深的鄙视。问我，到底想要干啥，目的是啥。是啊，我建站也就一个多月而已，想这些似乎早了点。而且，这种反复的折腾。把人搞的很分心，浪费了时间，甚至还会浪费金钱。真的到了那一天，自然更有动力和目的去解决问题，现在做这些，早了。</p>
<p>
	回到的我的博客，看到有人在本站下载页面给我留言。才发现那个页面，背景色一片大白，很是丑陋。不用想，肯定又是一大堆的垃圾样式代码，查看源码，直接去掉无效的样式，精简后，代码如下：(修改前的代码忘记保存了，至少是这个的三倍)</p>
<blockquote>
	<p>
		&lt;p&gt;关于本站主题的修改和来源，请参见：&lt;/p&gt;<br />
		&lt;h2&gt;&lt;a class=&quot;title&quot; href=&quot;http://www.coderli.com/archives/theme-diy-didiaoandhuali/&quot; rel=&quot;bookmark&quot;&gt;本站主题更新，基于《低调与华丽》DIY记录&lt;/a&gt;&lt;/h2&gt;<br />
		&lt;h2&gt;&lt;a class=&quot;title&quot; href=&quot;http://www.coderli.com/archives/theme-diy-didiaoandhuali-download/&quot; rel=&quot;bookmark&quot;&gt;本站主题下载 《低调与华丽&lt;/a&gt;&lt;/h2&gt;<br />
		&lt;p&gt;<br />
		这次是修改了一些问题后，重新上传开发下载。&lt;br/&gt;<br />
		只有下载的朋友，如果你没有安装wp-postviews插件，可能主题会显示不出来。这次主要修改了这个问题，注释掉了相关代码，你安装了插件后，即可打开。有问题，欢迎给我留言反馈。&lt;/p&gt;<br />
		&lt;p&gt;&lt;a href=&quot;http://www.coderli.com/wp-content/uploads/2012/07/didiaoandhuali.zip&quot;&gt;本站下载&lt;/a&gt;&lt;/p&gt;</p>
</blockquote>
<p>
	这不就是节省数据库空间么？再多的钱也能败光，先从养成良好的习惯做起的吧。</p>

