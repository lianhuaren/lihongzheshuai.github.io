---
layout: post
title: Windows下Libvirt Java API使用教程 - 开发环境部署
date: 2012-06-19 22:17
author: onecoder
comments: true
categories: [api, Java, Java进阶, Libvirt, libvirt, windows]
---
<div>
	Libvirt(<a href="http://libvirt.org/">http://libvirt.org/</a>)是一个比较不错的虚拟化环境管理的工具包。核心用c实现，不过提供了不同语言的调用API。官网的简介如下：</div>
<div>
	&nbsp;</div>
<div>
	<hr />
	<h2>
		libvirt is:</h2>
	<ul>
		<li>
			A toolkit to interact with the virtualization capabilities of recent versions of Linux (and other OSes), see our&nbsp;<a href="http://libvirt.org/goals.html">project goals</a>&nbsp;for details.</li>
		<li>
			Free software available under the&nbsp;<a href="http://www.opensource.org/licenses/lgpl-license.html">GNU Lesser General Public License</a>.</li>
		<li>
			A long term stable C API</li>
		<li>
			A set of bindings for common languages</li>
		<li>
			A&nbsp;<a href="http://libvirt.org/CIM/">CIM provider</a>&nbsp;for the DMTF virtualization schema</li>
		<li>
			A&nbsp;<a href="http://libvirt.org/qpid/">QMF agent</a>&nbsp;for the AMQP/QPid messaging system</li>
	</ul>
	<h2>
		libvirt supports:</h2>
	<ul>
		<li>
			The&nbsp;<a href="http://libvirt.org/drvqemu.html">KVM/QEMU</a>&nbsp;Linux hypervisor</li>
		<li>
			The&nbsp;<a href="http://libvirt.org/drvxen.html">Xen</a>&nbsp;hypervisor on Linux and Solaris hosts.</li>
		<li>
			The&nbsp;<a href="http://libvirt.org/drvlxc.html">LXC</a>&nbsp;Linux container system</li>
		<li>
			The&nbsp;<a href="http://libvirt.org/drvopenvz.html">OpenVZ</a>&nbsp;Linux container system</li>
		<li>
			The&nbsp;<a href="http://libvirt.org/drvuml.html">User Mode Linux</a>&nbsp;paravirtualized kernel</li>
		<li>
			The&nbsp;<a href="http://libvirt.org/drvvbox.html">VirtualBox</a>&nbsp;hypervisor</li>
		<li>
			The&nbsp;<a href="http://libvirt.org/drvesx.html">VMware ESX and GSX</a>&nbsp;hypervisors</li>
		<li>
			The&nbsp;<a href="http://libvirt.org/drvvmware.html">VMware Workstation and Player</a>&nbsp;hypervisors</li>
		<li>
			The&nbsp;<a href="http://libvirt.org/drvhyperv.html">Microsoft Hyper-V</a>&nbsp;hypervisor</li>
		<li>
			Virtual networks using bridging, NAT, VEPA and VN-LINK.</li>
		<li>
			Storage on IDE/SCSI/USB disks, FibreChannel, LVM, iSCSI, NFS and filesystems</li>
	</ul>
	<h2>
		libvirt provides:</h2>
	<ul>
		<li>
			Remote management using TLS encryption and x509 certificates</li>
		<li>
			Remote management authenticating with Kerberos and SASL</li>
		<li>
			Local access control using PolicyKit</li>
		<li>
			Zero-conf discovery using Avahi multicast-DNS</li>
		<li>
			Management of virtual machines, virtual networks and storage</li>
		<li>
			Portable client API for Linux, Solaris and Windows</li>
	</ul>
	<hr />
</div>
<div>
	&nbsp;</div>
<div>
	由于笔者我是一个简单而纯粹的Java程序员，所以自然只能依赖于libvirt的Java binding api。</div>
<div>
	作为一个源码控，我选择下载源码的方式验证使用：</div>
<div>
	源码的git地址如下：<br />
	<pre>
git clone git://libvirt.org/libvirt-java.git 
</pre>
</div>
<p>
	笔者下载源码后，直接构建了Eclipse的工程，当然你也可以用源码编译(ant)出一份jar来依赖：</p>
<pre>
cd libvirt-java
ant build
</pre>
<div>
	http://www.libvirt.org/maven2/ 没有Maven？可以直接从Maven库中下载Jar包：<br />
	<pre>
http://www.libvirt.org/maven2/org/libvirt/libvirt/
</pre>
	这么多途径，相信你总可以搞到一份libvirt的源码或者Jar了吧。<br />
	<div>
		由于libvirt的核心都是c写的，Java API只是帮助你封装了对动态链接库(dll)文件的本地调用，所以现在应该做的是安装dll文件。</div>
	<div>
		libvirt官网提供了自行编译dll文件的脚本：</div>
	<hr />
	<h3>
		The easiest way is to use the&nbsp;msys_setup&nbsp;script, developed by Matthias Bolte. This is actively developed and kept current with libvirt releases:</h3>
	<a href="https://github.com/photron/msys_setup">https://github.com/photron/msys_setup</a>&nbsp;
	<hr />
	不过笔者并没有尝试该种方式，因为libvirt官网也提供了windows下的安装包:</div>
<hr />
<h2>
	A windows installation package is in development. An experimental version is available here:</h2>
<p>
	<a href="http://libvirt.org/sources/win32_experimental/Libvirt-0.8.8-0.exe">http://libvirt.org/sources/win32_experimental/Libvirt-0.8.8-0.exe</a></p>
<p>
	It is not production ready.(注：其并不是已经发布的产品)</p>
<hr />
<p>
	该安装包中不仅包含了需要的dll文件，还提供了一个方便好用的virsh-shell 命令行工具，通过命令可以调用libvirt的所有接口(查看，管理虚拟机等。)，对于我们的开发调试是非常有帮助的。<br />
	<br />
	<input alt="virsh-shell.jpg" src="http://www.coderli.com/wp-content/uploads/2012/06/virsh-shell.jpg" type="image" /><br />
	<br />
	安装完成后，想让Java API找到dll文件，还需要指定jna路径，有两种方式，一种是直接设置系统环境变量：<br />
	<br />
	<img alt="" src="http://www.coderli.com/wp-content/uploads/2012/06/jna-path.jpg" /><br />
	<br />
	另一种是可在程序中动态指定，笔者选择了后者，比较灵活简单，编写测试代码如下：</p>
<br />
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
public void testGetXenVMs() {
		try {
			System.setProperty(&quot;jna.library.path&quot;, &quot;D:/Git-Repo/git/libvirt-java/libvirt-java/src/test/java/kubi/coder/&quot;);
			Connect conn = new Connect(&quot;xen+tcp://10.4.55.203/&quot;);
			System.out.println(conn.nodeInfo().cores);
			for (String name : conn.listDefinedDomains()) {
				System.out.println(name);
				if (name != null) {
					Domain domain = conn.domainLookupByName(name);
					System.out.println(domain.getMaxMemory());
					System.out.println(domain.getUUIDString());
					System.out.println(domain.getInfo().maxMem);
					System.out.println(domain.getInfo().state);
					System.out.println(conn.listDomains().length);
				}
			}
		} catch (LibvirtException e) {
			e.printStackTrace();
		}
	}
</pre>
<br />
<div>
	<div>
		是不是还是找不到dll？报异常？</div>
	<div>
		<hr />
		<pre>
Exception in thread &quot;main&quot; java.lang.UnsatisfiedLinkError: Unable to load library &#39;virt&#39;</pre>
	</div>
	<hr />
	原来他是搜索叫virt的dll文件。</div>
<div>
	<div>
		<br />
		查看源码：</div>
	<pre>
Libvirt INSTANCE = (Libvirt) Native.loadLibrary(&quot;virt&quot;, Libvirt.class);
</pre>
</div>
<hr />
<p>
	确实如此，将libvirt-0.dll改名为virt.dll。结果终于出来了。&nbsp;</p>
<div>
	&nbsp;</div>
<div>
	注：libvirt的Java API封装的比较直观，上手很容易，其入口就是Connect 这个连接类，获取连接后，即可对虚拟机环境进行查看和管理操作。笔者后续会奉上Java API详细使用介绍。</div>

