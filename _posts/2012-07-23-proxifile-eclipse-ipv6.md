---
layout: post
title: Eclipse,javaw 通过Proxifile代理ipv6协议问题解决
date: 2012-07-23 21:33
author: onecoder
comments: true
categories: [Eclipse, ipv6, proxifile]
---
这是一个在比较特殊情况下才会发生的问题。不过却在笔者的身上发生了，既然如此，那就记录一下，也许还有其他朋友也会碰到。<br />
<br />
问题描述：笔者办公环境使用Proxifile全局代码上网，不过对于一些不想走代理的程序和地址进行了过滤。然后笔者发现，Eclipse更新不好用了。查看Proxifile记录，发现Eclipse访问网址，都是通过ipv6协议。而ipv6似乎proxifile无法解析。于是，笔者在Eclipse的配置文件,eclipse.ini中加入下面一行配置。
<blockquote>
	<p>
		-vmargs<br />
		-Djava.net.preferIPv4Stack=true</p>
</blockquote>
重启，生效。<br />
<br />
然而，问题还没完。笔者最近一直在研究Netty。在启动Netty服务的时候，发现开始报无法绑定地址的错误。很显然，这也是由于设置proxifile代理产生的。（因为之前没有这个问题。），查看日志，还是ipv6的问题。这回是javaw.exe，走的ipv6协议。<br />
<br />
一样的解决办法，临时在启动项参数中加入：
<blockquote>
	<p>
		<span style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">-Djava.net.preferIPv4Stack=true</span></p>
</blockquote>
<span style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">解决。</span><br />
<br />
<span style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; "><img alt="vm-arguments" src="http://onecoder.qiniudn.com/8wuliao/C8umXXGs/11FR3Q.jpg" style="width: 584px; height: 392px; " /></span><br />
<br />
<span style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">不过后来笔者有考虑了一下，这样一个一个设置太麻烦了，不如来个全局的，于是笔者在使用的jre上，设置了全局参数。一劳永逸了。：）<br />
<img alt="jre-arguments" src="http://onecoder.qiniudn.com/8wuliao/C8umXV3c/6g8sc.jpg" style="width: 635px; height: 248px; " /></span>
