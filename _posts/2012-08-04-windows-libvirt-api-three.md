---
layout: post
title: Windows下Libvirt Java API使用教程(三)- TLS认证访问和动态连接文件依赖
date: 2012-08-04 23:39
author: onecoder
comments: true
categories: [Java, Libvirt, libvirt, windows]
---
<p>
	突兀的出来一个libvirt的教程三，您可能会觉得奇怪，其实这是OneCoder以前写的一个小系列教程，原来发在51cto的博客上，前两篇已经发了过来，考虑到完整性，就把第三篇也发过来。</p>
<p>
	前两篇地址：</p>
<p>
	《<a href="http://www.coderli.com/archives/windows-libvirt-one/">Windows下Libvirt Java API使用教程 &ndash; 开发环境部署</a>》</p>
<p>
	《<a href="http://www.coderli.com/archives/windows-libvirt-api-two/">Windows下Libvirt Java API使用教程(二)- 接口使用说明</a>》</p>
<p>
	之前已经介绍过了libvirt api的上手使用方式，这里再补充一些细节问题。</p>
<p>
	&nbsp;</p>
<p>
	TLS安全认证访问：<br />
	之前我们给出的例子都是直接用tcp访问的，这就需要被访问的服务器开放tcp访问的端口，也就是说，任何机器只要知道了服务器的ip，都是可以访问上面的libvirt接口的。这是十分危险的，在生产环境中是不可取的。<br />
	所以，libvirt支持基于tls认证的安全访问协议。连接格式如下：</p>
<p>
	&nbsp;</p>
<blockquote>
	<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
		qemu+tls://10.4.54.10<br />
		或者<br />
		qemu://10.4.54.10(因为默认就是走tls的)</div>
</blockquote>
<div>
	要想正常访问，必须在客户端和服务端配置证书，下面就介绍一个这个配置过程。
	<div>
		&nbsp;</div>
	<div>
		1、首先自然是证书生成工具的安装。libvirt官方推荐的工具是：GnuTLS。不过可惜，官网给出的地然居然不正确。GnuTLS项目，官网地址如下：</div>
	<div>
		<a href="http://www.gnu.org/software/gnutls/manual/html_node/">http://www.gnu.org/software/gnutls/manual/html_node/</a></div>
	<div>
		&nbsp;</div>
	<div>
		下载页面为：</div>
	<div>
		<a href="http://www.gnu.org/software/gnutls/download.html">http://www.gnu.org/software/gnutls/download.html</a></div>
	<div>
		&nbsp;</div>
	<div>
		这里介绍的windows环境下配置，所以进入页面：</div>
	<div>
		<a href="http://homes.esat.kuleuven.be/~nikos/gnutls-win32/">http://homes.esat.kuleuven.be/~nikos/gnutls-win32/</a>&nbsp;下载即可。</div>
	<div>
		&nbsp;</div>
	<div>
		2、解压下载的工具。通过命令行进入\bin目录(或者将该bin目录配置到环境变量的path即可在任意路径访问)：</div>
</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: center; ">
	<img alt="" src="http://onecoder.qiniudn.com/8wuliao/CalKLekN/GyTui.jpg" /></div>
<div style="font-family: Tahoma; font-size: 14px; ">
	3、首先为你的证书生成私钥：
	<p>
		Create a private key for your CA:</p>
	<blockquote>
		<p>
			certtool --generate-privkey &gt; cakey.pem</p>
	</blockquote>
	<p style="text-align: center; ">
		<img alt="" src="http://onecoder.qiniudn.com/8wuliao/CalKLfpd/15v6oh.jpg" style="width: 673px; height: 263px; " /></p>
	<p>
		然后，为你证书进行自签名。<br />
		and self-sign it by creating a file with the signature details called ca.info containing:</p>
	<blockquote>
		<p>
			cn = Name of your organization<br />
			ca<br />
			cert_signing_key</p>
		<p>
			certtool --generate-self-signed --load-privkey cakey.pem \<br />
			&nbsp; --template ca.info --outfile cacert.pem (Y</p>
	</blockquote>
</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	先在当前路径下创建一个叫ca.info的文件，里面写上如上第一段内容，</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: center; ">
	<img alt="" src="http://onecoder.qiniudn.com/8wuliao/CalKKEiy/dcm8W.jpg" /></div>
<div style="font-family: Tahoma; font-size: 14px; ">
	然后执行第二段引用所示命令：</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: center; ">
	<img alt="" src="http://onecoder.qiniudn.com/8wuliao/CalKLelt/medium.jpg" /></div>
<div style="font-family: Tahoma; font-size: 14px; ">
	报错。这下头疼了。。笔者翻箱倒柜，翻江倒海找了一通。。实在没有找到答案。。。考虑到证书的制作过程中没有与机器相关的信息，所以，决定求助笔者非常不熟悉的linux。掏出putty，登录，重新执行上面第一个命令。生成私钥：</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: center; ">
	<img alt="" src="http://onecoder.qiniudn.com/8wuliao/CalKLA1S/nT1dV.jpg" /></div>
<div style="font-family: Tahoma; font-size: 14px; ">
	然后同样按照上面的方式进行签名，果然轻松成功！</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: center; ">
	<img alt="" src="http://onecoder.qiniudn.com/8wuliao/CalKLP2I/cMkws.jpg" /></div>
<div style="font-family: Tahoma; font-size: 14px; ">
	此时已经可以删除ca.info文件了。继续生成证书，接下来开始生成服务端私钥：</div>
<blockquote>
	<div style="font-family: Tahoma; font-size: 14px; ">
		certtool --generate-privkey &gt; serverkey.pem</div>
</blockquote>
<div style="font-family: Tahoma; font-size: 14px; text-align: center; ">
	<img alt="" src="http://onecoder.qiniudn.com/8wuliao/CalKLA1S/nT1dV.jpg" style="width: 668px; height: 439px; " /></div>
<div style="font-family: Tahoma; font-size: 14px; ">
	然后签名：</div>
<blockquote>
	<div style="font-family: Tahoma; font-size: 14px; ">
		certtool --generate-certificate --load-privkey serverkey.pem \<br />
		&nbsp; --load-ca-certificate cacert.pem --load-ca-privkey cakey.pem \<br />
		&nbsp; --template server.info --outfile servercert.pem</div>
</blockquote>
<p>
	同样是依靠一个叫做server.info的文件。先创建改文件，写入如下格式内容：</p>
<blockquote>
	<p>
		organization = Name of your organization<br />
		cn = oirase<br />
		tls_www_server<br />
		encryption_key<br />
		signing_key</p>
</blockquote>
<p>
	这里只有cn这个属性需要注意，他应该是当前主机的主机名：（only the CN field matters, which as explained above must be the server&#39;s hostname）- (The CN must match the hostname which clients will be using to connect to the server. In the example below, clients will be connecting to the server using a URI of xen://oirase/, so the CN must be &quot;oirase&quot;.)</p>
<p>
	如果不知道主机名，用ip当然也是可以的。那么你的访问就是通过IP访问了。：）</p>
<p style="text-align: center; ">
	<img alt="" src="http://onecoder.qiniudn.com/8wuliao/CalKM6NF/Obxaz.jpg" /></p>
<p>
	成功。</p>
<p>
	将服务端证书拷贝服务器的相应位置：</p>
<blockquote>
	<p>
		Finally we have two files to install:</p>
	<p>
		serverkey.pem is the server&#39;s private key which should be copied to the server only as /etc/pki/libvirt/private/serverkey.pem.<br />
		servercert.pem is the server&#39;s certificate which can be installed on the server as /etc/pki/libvirt/servercert.pem.</p>
</blockquote>
<p>
	最后是客户端的证书，过程类似，这里只列出主要过程和命令：</p>
<ol>
	<li>
		创建私钥:</li>
</ol>
<blockquote>
	<p>
		certtool --generate-privkey &gt; clientkey.pem</p>
</blockquote>
<ol start="2">
	<li>
		创建client.info模版文件:</li>
</ol>
<blockquote>
	<p>
		country = GB<br />
		state = London<br />
		locality = London<br />
		organization = Red Hat<br />
		cn = client1<br />
		tls_www_client<br />
		encryption_key<br />
		signing_key</p>
</blockquote>
<p>
	&nbsp;&nbsp;&nbsp;&nbsp;然后签名:</p>
<blockquote>
	<p>
		certtool --generate-certificate --load-privkey clientkey.pem \<br />
		&nbsp; --load-ca-certificate cacert.pem --load-ca-privkey cakey.pem \<br />
		&nbsp; --template client.info --outfile clientcert.pem</p>
</blockquote>
<ol start="3" style="">
	<li>
		拷贝证书到客户端的指定位置（linux）:</li>
</ol>
<blockquote>
	<p>
		cp clientkey.pem /etc/pki/libvirt/private/clientkey.pem<br />
		cp clientcert.pem /etc/pki/libvirt/clientcert.pem</p>
</blockquote>
<p>
	其中client.info中国家等信息可根据你的情况指定。</p>
<p>
	至此，需要的证书已经都准备好了。证书有了，服务端也部署了。但是windows的客户端不知道该部署到什么地方？因为网上的说明给出的都是linux文件路径，没说windows下的情况。不过笔者相信libvirt的错误日志：）于是：</p>
<p style="text-align: center; ">
	<img alt="" src="http://onecoder.qiniudn.com/8wuliao/CalKMmeC/mDDtR.jpg" style="width: 674px; height: 301px; " /></p>
<p>
	怎么样，位置有了吧。赶紧拷贝过去试试。</p>
<p>
	有的同学可能发现，这里只给出了一个位置，我们有三个证书呢。别急，你看这个路径，比照网上给出的linux的路径格式：</p>
<p>
	&nbsp;</p>
<table>
	<tbody>
		<tr>
			<th>
				Location</th>
			<th>
				Machine</th>
			<th>
				Description</th>
			<th>
				Required fields</th>
		</tr>
		<tr>
			<td>
				/etc/pki/CA/cacert.pem</td>
			<td>
				Installed on all clients and servers</td>
			<td>
				CA&#39;s certificate (<a href="http://libvirt.org/remote.html#Remote_TLS_CA">more info</a>)</td>
			<td>
				n/a</td>
		</tr>
		<tr>
			<td>
				/etc/pki/libvirt/ private/serverkey.pem</td>
			<td>
				Installed on the server</td>
			<td>
				Server&#39;s private key (<a href="http://libvirt.org/remote.html#Remote_TLS_server_certificates">more info</a>)</td>
			<td>
				n/a</td>
		</tr>
		<tr>
			<td>
				/etc/pki/libvirt/ servercert.pem</td>
			<td>
				Installed on the server</td>
			<td>
				Server&#39;s certificate signed by the CA. (<a href="http://libvirt.org/remote.html#Remote_TLS_server_certificates">more info</a>)</td>
			<td>
				CommonName (CN) must be the hostname of the server as it is seen by clients.</td>
		</tr>
		<tr>
			<td>
				/etc/pki/libvirt/ private/clientkey.pem</td>
			<td>
				Installed on the client</td>
			<td>
				Client&#39;s private key. (<a href="http://libvirt.org/remote.html#Remote_TLS_client_certificates">more info</a>)</td>
			<td>
				n/a</td>
		</tr>
		<tr>
			<td>
				/etc/pki/libvirt/ clientcert.pem</td>
			<td>
				Installed on the client</td>
			<td>
				Client&#39;s certificate signed by the CA (<a href="http://libvirt.org/remote.html#Remote_TLS_client_certificates">more info</a>)</td>
			<td>
				Distinguished Name (DN) can be checked against an access control list (tls_allowed_dn_list).</td>
		</tr>
	</tbody>
</table>
<p>
	其实基本已经可以猜出windows下的目录组织了。linux在的/etc目录，就想当于我们这里的..\libvirt目录，子目录的组织相同即可。</p>
<p>
	没猜出来也不用担心，最多我们复制一个证书尝试一次，错误信息会依次告诉你所有的路径的：）</p>
<p>
	再访问一下，成功~</p>
<p style="text-align: center; ">
	<img alt="" src="http://onecoder.qiniudn.com/8wuliao/CalKKYOd/5WsWD.jpg" /></p>
<p>
	补充说明一下关于动态链接文件的问题:<br />
	之前介绍的时候，笔者只提到说调用libvirt Java api需要一个virt.dl文件，这个文件笔者是拷贝的libvirt-0.dll文件改名而来，然后访问成功。</p>
<p>
	笔者后来发现，原来libvirt 安装目录的bin文件夹下的所有文件，其实都是改dll文件的依赖文件，当笔者将其他文件删除或转义的时候，依然会报找不到virt.dll文件的错误。所以，如果调用libvirt Java API开发，可以将所有的dll拷贝到一个指定位置，然后指定jna.library.path到该位置即可。</p>
<p>
	&nbsp;</p>
<p>
	&nbsp;</p>

