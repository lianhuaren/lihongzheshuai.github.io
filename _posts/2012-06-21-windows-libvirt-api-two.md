---
layout: post
title: Windows下Libvirt Java API使用教程(二)- 接口使用说明
date: 2012-06-21 23:05
author: onecoder
comments: true
categories: [api, Java, Libvirt, libvirt, windows]
---
介绍完libvirt Java API的部署工作，接下来我们就介绍一下接口的使用和代码样例。<br />
<br />
libvirt的管理单位是单个主机，所以探测和监控接口所能获取的信息的最大范围也是主机。所以先从主机入手，验证libvirt接口。主机（libvirt所在管理节点）探测相关接口验证代码如下：<br />
<br />
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
   @Before
	public void init() {
		System.setProperty(&quot;jna.library.path&quot;,
				&quot;D:/Git-Repo/git/libvirt-java/libvirt-java/src/test/java/kubi/coder/&quot;);
		try {
			xenConn = new Connect(&quot;xen+tcp://10.4.55.203/&quot;);
			// system代表拥有系统权限/session是用户权限
			kvmConn = new Connect(&quot;qemu+tcp://10.4.54.10/system&quot;);
		} catch (LibvirtException e) {
			e.printStackTrace();
		}
	}

	/**
	 * 主机信息探测接口验证，验证可以获取的主机属性和监控指标，分别考虑Xen环境和KVM环境
	 * 
	 * 
	 * @author lihzh
	 * @date 2012-5-15 下午1:28:00
	 */
	@Test
	public void testDetectHost() {
		// KVM
		doDetectHost(kvmConn);
		// XEN
		doDetectHost(xenConn);
	}

	/**
	 * 执行探测主机测试函数
	 * 
	 * @param conn
	 * @author lihzh
	 * @date 2012-5-15 下午1:37:37
	 */
	private void doDetectHost(Connect conn) {
		try {
			// Returns the free memory for the connection
			// System.out.println(&quot;FreeMemory: &quot; + conn.getFreeMemory());// 不支持
			
			// Returns the system hostname on which the hypervisor is running.
			// (the result of the gethostname(2) system call)
			// If we are connected to a remote system,
			// then this returns the hostname of the remote system
			System.out.println(&quot;Host name: &quot; + conn.getHostName());
			// Gets the name of the Hypervisor software used.
			System.out.println(&quot;Type: &quot; + conn.getType());
			// Gets the version level of the Hypervisor running. This may work
			// only with hypervisor call, i.e. with priviledged access to the
			// hypervisor, not with a Read-Only connection. If the version can&#39;t
			// be extracted by lack of capacities returns 0.
			// Returns:
			// major * 1,000,000 + minor * 1,000 + release
			System.out.println(conn.getVersion());

			NodeInfo nodeInfo = conn.nodeInfo();
			System.out.println(&quot;the number of active CPUs: &quot; + nodeInfo.cpus);
			System.out.println(&quot;number of core per socket: &quot; + nodeInfo.cores);
			System.out.println(&quot;memory size in kilobytes: &quot; + nodeInfo.memory);
			System.out.println(&quot;expected CPU frequency: &quot; + nodeInfo.mhz);
			System.out.println(&quot;string indicating the CPU model: &quot;
					+ nodeInfo.model);
			System.out.println(&quot;the number of NUMA cell, 1 for uniform: &quot;
					+ nodeInfo.nodes);
			System.out.println(&quot;number of CPU socket per node: &quot;
					+ nodeInfo.sockets);
			System.out.println(&quot;number of threads per core: &quot;
					+ nodeInfo.threads);
			System.out
					.println(&quot;the total number of CPUs supported but not necessarily active in the host.: &quot;
							+ nodeInfo.maxCpus());

			// for (String interName : conn.listInterfaces()) {
			// System.out.println(interName);
			// } 不支持

			// Provides the list of names of defined interfaces on this host

			// for (String interName : conn.listDefinedInterfaces()) {
			// System.out.println(interName);
			// } // 不支持

			// Lists the active networks.
			for (String networkName : conn.listNetworks()) {
				System.out.println(&quot;Network name: &quot; + networkName);
			}

			// Lists the names of the network filters
			for (String networkFilterName : conn.listNetworkFilters()) {
				System.out.println(&quot;Network filter name: &quot; + networkFilterName);
			}

			System.out.println(conn.getCapabilities());
		} catch (LibvirtException e) {
			e.printStackTrace();
		}
	}
</pre>
<br />
&nbsp;分别在KVM和XEN环境下测试了libvirt接口，测试结果如下：<br />
<br />
<pre class="brush:xml;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
Host name: s5410
Type: QEMU
9001
the number of active CPUs: 64
number of core per socket: 8
memory size in kilobytes: 49444896
expected CPU frequency: 2131
string indicating the CPU model: x86_64
the number of NUMA cell, 1 for uniform: 1
number of CPU socket per node: 4
number of threads per core: 2
the total number of CPUs supported but not necessarily active in the host.: 64
Network name: hello
Network name: default
Network filter name: no-other-l2-traffic
Network filter name: allow-dhcp
Network filter name: allow-dhcp-server
Network filter name: no-other-rarp-traffic
Network filter name: no-mac-spoofing
Network filter name: qemu-announce-self-rarp
Network filter name: clean-traffic
Network filter name: no-arp-spoofing
Network filter name: allow-ipv4
Network filter name: no-ip-spoofing
Network filter name: qemu-announce-self
Network filter name: allow-arp
Network filter name: no-mac-broadcast
Network filter name: allow-incoming-ipv4
Network filter name: no-ip-multicast
&lt;capabilities&gt;

  &lt;host&gt;
    &lt;uuid&gt;30b940dd-f79a-21a2-82d5-ddc1b1b4a7e4&lt;/uuid&gt;
    &lt;cpu&gt;
      &lt;arch&gt;x86_64&lt;/arch&gt;
      &lt;model&gt;core2duo&lt;/model&gt;
      &lt;topology sockets=&#39;4&#39; cores=&#39;8&#39; threads=&#39;2&#39;/&gt;
      &lt;feature name=&#39;lahf_lm&#39;/&gt;
      &lt;feature name=&#39;rdtscp&#39;/&gt;
      &lt;feature name=&#39;pdpe1gb&#39;/&gt;
      &lt;feature name=&#39;popcnt&#39;/&gt;
      &lt;feature name=&#39;x2apic&#39;/&gt;
      &lt;feature name=&#39;sse4.2&#39;/&gt;
      &lt;feature name=&#39;sse4.1&#39;/&gt;
      &lt;feature name=&#39;dca&#39;/&gt;
      &lt;feature name=&#39;xtpr&#39;/&gt;
      &lt;feature name=&#39;cx16&#39;/&gt;
      &lt;feature name=&#39;tm2&#39;/&gt;
      &lt;feature name=&#39;est&#39;/&gt;
      &lt;feature name=&#39;vmx&#39;/&gt;
      &lt;feature name=&#39;ds_cpl&#39;/&gt;
      &lt;feature name=&#39;pbe&#39;/&gt;
      &lt;feature name=&#39;tm&#39;/&gt;
      &lt;feature name=&#39;ht&#39;/&gt;
      &lt;feature name=&#39;ss&#39;/&gt;
      &lt;feature name=&#39;acpi&#39;/&gt;
      &lt;feature name=&#39;ds&#39;/&gt;
    &lt;/cpu&gt;
    &lt;migration_features&gt;
      &lt;live/&gt;
      &lt;uri_transports&gt;
        &lt;uri_transport&gt;tcp&lt;/uri_transport&gt;
      &lt;/uri_transports&gt;
    &lt;/migration_features&gt;
  &lt;/host&gt;

  &lt;guest&gt;
    &lt;os_type&gt;hvm&lt;/os_type&gt;
    &lt;arch name=&#39;i686&#39;&gt;
      &lt;wordsize&gt;32&lt;/wordsize&gt;
      &lt;emulator&gt;/usr/libexec/qemu-kvm&lt;/emulator&gt;
      &lt;machine&gt;rhel5.4.0&lt;/machine&gt;
      &lt;machine canonical=&#39;rhel5.4.0&#39;&gt;pc&lt;/machine&gt;
      &lt;machine&gt;rhel5.4.4&lt;/machine&gt;
      &lt;machine&gt;rhel5.5.0&lt;/machine&gt;
      &lt;machine&gt;rhel5.6.0&lt;/machine&gt;
      &lt;domain type=&#39;qemu&#39;&gt;
      &lt;/domain&gt;
      &lt;domain type=&#39;kvm&#39;&gt;
        &lt;emulator&gt;/usr/libexec/qemu-kvm&lt;/emulator&gt;
      &lt;/domain&gt;
    &lt;/arch&gt;
    &lt;features&gt;
      &lt;cpuselection/&gt;
      &lt;pae/&gt;
      &lt;nonpae/&gt;
      &lt;acpi default=&#39;on&#39; toggle=&#39;yes&#39;/&gt;
      &lt;apic default=&#39;on&#39; toggle=&#39;no&#39;/&gt;
    &lt;/features&gt;
  &lt;/guest&gt;

  &lt;guest&gt;
    &lt;os_type&gt;hvm&lt;/os_type&gt;
    &lt;arch name=&#39;x86_64&#39;&gt;
      &lt;wordsize&gt;64&lt;/wordsize&gt;
      &lt;emulator&gt;/usr/libexec/qemu-kvm&lt;/emulator&gt;
      &lt;machine&gt;rhel5.4.0&lt;/machine&gt;
      &lt;machine canonical=&#39;rhel5.4.0&#39;&gt;pc&lt;/machine&gt;
      &lt;machine&gt;rhel5.4.4&lt;/machine&gt;
      &lt;machine&gt;rhel5.5.0&lt;/machine&gt;
      &lt;machine&gt;rhel5.6.0&lt;/machine&gt;
      &lt;domain type=&#39;qemu&#39;&gt;
      &lt;/domain&gt;
      &lt;domain type=&#39;kvm&#39;&gt;
        &lt;emulator&gt;/usr/libexec/qemu-kvm&lt;/emulator&gt;
      &lt;/domain&gt;
    &lt;/arch&gt;
    &lt;features&gt;
      &lt;cpuselection/&gt;
      &lt;acpi default=&#39;on&#39; toggle=&#39;yes&#39;/&gt;
      &lt;apic default=&#39;on&#39; toggle=&#39;no&#39;/&gt;
    &lt;/features&gt;
  &lt;/guest&gt;

&lt;/capabilities&gt;

Host name: s55203
Type: Xen
3001000
the number of active CPUs: 32
number of core per socket: 8
memory size in kilobytes: 50276352
expected CPU frequency: 1995
string indicating the CPU model: x86_64
the number of NUMA cell, 1 for uniform: 1
number of CPU socket per node: 2
number of threads per core: 2
the total number of CPUs supported but not necessarily active in the host.: 32
Network name: default
Network filter name: allow-dhcp-server
Network filter name: qemu-announce-self-rarp
Network filter name: no-arp-spoofing
Network filter name: allow-arp
Network filter name: no-ip-multicast
Network filter name: no-other-rarp-traffic
Network filter name: allow-incoming-ipv4
Network filter name: no-mac-spoofing
Network filter name: allow-ipv4
Network filter name: no-ip-spoofing
Network filter name: clean-traffic
Network filter name: qemu-announce-self
Network filter name: no-other-l2-traffic
Network filter name: allow-dhcp
Network filter name: no-mac-broadcast
&lt;capabilities&gt;

  &lt;host&gt;
    &lt;cpu&gt;
      &lt;arch&gt;x86_64&lt;/arch&gt;
      &lt;features&gt;
        &lt;vmx/&gt;
      &lt;/features&gt;
    &lt;/cpu&gt;
    &lt;migration_features&gt;
      &lt;live/&gt;
      &lt;uri_transports&gt;
        &lt;uri_transport&gt;xenmigr&lt;/uri_transport&gt;
      &lt;/uri_transports&gt;
    &lt;/migration_features&gt;
  &lt;/host&gt;

  &lt;guest&gt;
    &lt;os_type&gt;xen&lt;/os_type&gt;
    &lt;arch name=&#39;x86_64&#39;&gt;
      &lt;wordsize&gt;64&lt;/wordsize&gt;
      &lt;emulator&gt;/usr/lib64/xen/bin/qemu-dm&lt;/emulator&gt;
      &lt;machine&gt;xenpv&lt;/machine&gt;
      &lt;domain type=&#39;xen&#39;&gt;
      &lt;/domain&gt;
    &lt;/arch&gt;
  &lt;/guest&gt;

  &lt;guest&gt;
    &lt;os_type&gt;xen&lt;/os_type&gt;
    &lt;arch name=&#39;i686&#39;&gt;
      &lt;wordsize&gt;32&lt;/wordsize&gt;
      &lt;emulator&gt;/usr/lib64/xen/bin/qemu-dm&lt;/emulator&gt;
      &lt;machine&gt;xenpv&lt;/machine&gt;
      &lt;domain type=&#39;xen&#39;&gt;
      &lt;/domain&gt;
    &lt;/arch&gt;
    &lt;features&gt;
      &lt;pae/&gt;
    &lt;/features&gt;
  &lt;/guest&gt;

  &lt;guest&gt;
    &lt;os_type&gt;hvm&lt;/os_type&gt;
    &lt;arch name=&#39;i686&#39;&gt;
      &lt;wordsize&gt;32&lt;/wordsize&gt;
      &lt;emulator&gt;/usr/lib64/xen/bin/qemu-dm&lt;/emulator&gt;
      &lt;loader&gt;/usr/lib/xen/boot/hvmloader&lt;/loader&gt;
      &lt;machine&gt;xenfv&lt;/machine&gt;
      &lt;domain type=&#39;xen&#39;&gt;
      &lt;/domain&gt;
    &lt;/arch&gt;
    &lt;features&gt;
      &lt;pae/&gt;
      &lt;nonpae/&gt;
      &lt;acpi default=&#39;on&#39; toggle=&#39;yes&#39;/&gt;
      &lt;apic default=&#39;on&#39; toggle=&#39;yes&#39;/&gt;
    &lt;/features&gt;
  &lt;/guest&gt;

  &lt;guest&gt;
    &lt;os_type&gt;hvm&lt;/os_type&gt;
    &lt;arch name=&#39;x86_64&#39;&gt;
      &lt;wordsize&gt;64&lt;/wordsize&gt;
      &lt;emulator&gt;/usr/lib64/xen/bin/qemu-dm&lt;/emulator&gt;
      &lt;loader&gt;/usr/lib/xen/boot/hvmloader&lt;/loader&gt;
      &lt;machine&gt;xenfv&lt;/machine&gt;
      &lt;domain type=&#39;xen&#39;&gt;
      &lt;/domain&gt;
    &lt;/arch&gt;
    &lt;features&gt;
      &lt;acpi default=&#39;on&#39; toggle=&#39;yes&#39;/&gt;
      &lt;apic default=&#39;on&#39; toggle=&#39;yes&#39;/&gt;
    &lt;/features&gt;
  &lt;/guest&gt;

&lt;/capabilities&gt;
</pre>
<font color="#ff0000" style="font-family: Tahoma; font-size: 14px; ">注1</font><span style="font-family: Tahoma; font-size: 14px; ">：标注不支持的，是在当前环境当前libvirt版本下，运行会报：</span><br />
<div style="font-family: Tahoma; font-size: 14px; ">
	<div>
		<span style="color:#ff0000;">&nbsp;unsupported in sys interface</span><br />
		的接口。<br />
		&nbsp;</div>
	<div>
		<font color="#ff0000">注2</font>：名词解释<br />
		<br />
		<pre class="brush:shell;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
hvm:
gives similar information but when running a 32 bit OS fully virtualized with Xen using the hvm support。

numa：
For processors that support hyperthreading, this is the number of hyperthreads
they have per core.  On a machine that doesn&#39;t support hyperthreading, this
will be 1.
</pre>
	</div>
</div>
<br />
说实话，这么多信息中，笔者关注的不多，有很多甚至说不出其代表的含义，笔者关注只是cpu的个数，核心数，内存总量等直观信息。有就足以。<br />
看完了主机，再看看虚拟机。一个虚拟化环境中的核心资源自然是虚拟机，所以其属性和信息也自然多很多，上测试代码：<br />
<br />
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
/**
	 * 测试探测虚拟机接口
	 * 
	 * @author lihzh
	 * @date 2012-5-16 上午11:14:20
	 */
	@Test
	public void testDetectDomains() {
		// KVM
		doDetectDomains(kvmConn);
		// XEN
		doDetectDomains(xenConn);
	}

        /**
	 * 执行探测虚拟机测试函数
	 * 
	 * @param conn
	 * @author lihzh
	 * @date 2012-5-16 上午11:15:27
	 */
	private void doDetectDomains(Connect conn) {
		try {
			// Lists the active domains.(列出所有处于启动(激活)状态的虚拟机的id)
			for (int activeDomId : conn.listDomains()) {
				System.out.println(&quot;Active vm id: &quot; + activeDomId);
				// 根据Id，探测各个虚拟机的详细信息
				Domain domain = conn.domainLookupByID(activeDomId);
				// Gets the hypervisor ID number for the domain
				System.out.println(&quot;Domain id: &quot; + domain.getID());
				// Gets the public name for this domain
				System.out.println(&quot;Domain name: &quot; + domain.getName());
				// Gets the type of domain operation system.
				System.out.println(&quot;Domain os type: &quot; + domain.getOSType());
				// Gets the UUID for this domain as string.
				System.out.println(&quot;Domain uuid: &quot; + domain.getUUIDString());
				// Retrieve the maximum amount of physical memory allocated to a
				// domain.
				System.out.println(&quot;Domain max memory: &quot;
						+ domain.getMaxMemory());
				// Provides the maximum number of virtual CPUs supported for the
				// guest VM. If the guest is inactive, this is basically the
				// same as virConnectGetMaxVcpus. If the guest is running this
				// will reflect the maximum number of virtual CPUs the guest was
				// booted with.
				System.out.println(&quot;Domain max vcpu: &quot; + domain.getMaxVcpus());
				// Provides an XML description of the domain. The description
				// may be
				// reused later to relaunch the domain with createLinux().
				System.out.println(&quot;Domain xml description: &quot;
						+ domain.getXMLDesc(0));
				System.out.println(&quot;Domain maxMen allowed: &quot;
						+ domain.getInfo().maxMem);
				System.out.println(&quot;Domain memory: &quot; + domain.getInfo().memory);
				// domain.getJobInfo()
				// 不支持
				System.out.println(&quot;Domain state: &quot; + domain.getInfo().state);
				// Provides a boolean value indicating whether the network is
				// configured to be automatically started when the host machine
				// boots.
				System.out.println(&quot;Domain network autostart: &quot;
						+ domain.getAutostart());
				// Extracts information about virtual CPUs of this domain
				for (VcpuInfo vcpuInfo : domain.getVcpusInfo()) {
					System.out.println(&quot;cpu: &quot; + vcpuInfo.cpu);
					System.out.println(&quot;cpu time: &quot; + vcpuInfo.cpuTime);
					System.out.println(&quot;cpu number: &quot; + vcpuInfo.number);
					System.out.println(&quot;cpu state: &quot; + vcpuInfo.state);
				}
			
				// 如果是KVM环境
				if (conn.getURI().startsWith(&quot;qemu&quot;)) {
					// This function returns block device (disk) stats for block
					// devices attached to the domain
					DomainBlockInfo blockInfo = domain
							.blockInfo(&quot;/opt/awcloud/instance/admin/&quot;
									+ domain.getName() + &quot;/disk&quot;);
					System.out.println(&quot;Disk Capacity: &quot;
							+ blockInfo.getCapacity());
					System.out.println(&quot;Disk allocation: &quot;
							+ blockInfo.getAllocation());
					System.out.println(&quot;Disk physical: &quot;
							+ blockInfo.getPhysical());

					DomainBlockStats blockStats = domain.blockStats(&quot;vda&quot;);
					// 磁盘读取请求总数
					System.out.println(&quot;read request num: &quot; + blockStats.rd_req);
					// 磁盘读取总bytes数
					System.out.println(&quot;read request num: &quot; + blockStats.rd_bytes);
					// 磁盘写入请求总数
					System.out.println(&quot;read request num: &quot; + blockStats.wr_req);
					// 磁盘写入总bytes数
					System.out.println(&quot;read request num: &quot; + blockStats.wr_bytes);
				}

			}
			
			// 列出所有停止态的虚拟机
			for (String name : conn.listDefinedDomains()) {
				System.out.println(&quot;Inactive domain name: &quot; + name);
			}

		} catch (LibvirtException e) {
			e.printStackTrace();
		}
	}

</pre>
<br />
循环较多，摘取部分测试结果如下：<br />
<br />
<pre class="brush:xml;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
Active vm id: 53
Domain id: 53
Domain name: i-546A099E
Domain os type: hvm
Domain uuid: e608560a-2c03-8e48-2e60-d0d01693f530
Domain max memory: 147456
Domain max vcpu: 1
Domain xml description: &lt;domain type=&#39;xen&#39; id=&#39;53&#39;&gt;
  &lt;name&gt;i-546A099E&lt;/name&gt;
  &lt;uuid&gt;e608560a-2c03-8e48-2e60-d0d01693f530&lt;/uuid&gt;
  &lt;memory&gt;131072&lt;/memory&gt;
  &lt;currentMemory&gt;131072&lt;/currentMemory&gt;
  &lt;vcpu&gt;1&lt;/vcpu&gt;
  &lt;os&gt;
    &lt;type&gt;hvm&lt;/type&gt;
    &lt;loader&gt;/usr/lib/xen/boot/hvmloader&lt;/loader&gt;
    &lt;boot dev=&#39;hd&#39;/&gt;
  &lt;/os&gt;
  &lt;features&gt;
    &lt;acpi/&gt;
    &lt;apic/&gt;
    &lt;pae/&gt;
  &lt;/features&gt;
  &lt;clock offset=&#39;utc&#39;/&gt;
  &lt;on_poweroff&gt;destroy&lt;/on_poweroff&gt;
  &lt;on_reboot&gt;restart&lt;/on_reboot&gt;
  &lt;on_crash&gt;restart&lt;/on_crash&gt;
  &lt;devices&gt;
    &lt;emulator&gt;/usr/lib64/xen/bin/qemu-dm&lt;/emulator&gt;
    &lt;disk type=&#39;file&#39; device=&#39;disk&#39;&gt;
      &lt;driver name=&#39;file&#39;/&gt;
      &lt;source file=&#39;/opt/awcloud/instance/admin/i-546A099E/disk&#39;/&gt;
      &lt;target dev=&#39;hda&#39; bus=&#39;ide&#39;/&gt;
    &lt;/disk&gt;
    &lt;disk type=&#39;file&#39; device=&#39;disk&#39;&gt;
      &lt;driver name=&#39;file&#39;/&gt;
      &lt;source file=&#39;/opt/awcloud/instance/admin/i-546A099E/disk2&#39;/&gt;
      &lt;target dev=&#39;hdb&#39; bus=&#39;ide&#39;/&gt;
    &lt;/disk&gt;
    &lt;interface type=&#39;bridge&#39;&gt;
      &lt;mac address=&#39;d0:0d:54:6a:09:9e&#39;/&gt;
      &lt;source bridge=&#39;xenbr0&#39;/&gt;
      &lt;script path=&#39;vif-bridge&#39;/&gt;
      &lt;target dev=&#39;vif53.0&#39;/&gt;
    &lt;/interface&gt;
    &lt;serial type=&#39;file&#39;&gt;
      &lt;source path=&#39;/opt/awcloud/instance/admin/i-546A099E/console.log&#39;/&gt;
      &lt;target port=&#39;0&#39;/&gt;
    &lt;/serial&gt;
    &lt;console type=&#39;file&#39;&gt;
      &lt;source path=&#39;/opt/awcloud/instance/admin/i-546A099E/console.log&#39;/&gt;
      &lt;target port=&#39;0&#39;/&gt;
    &lt;/console&gt;
    &lt;input type=&#39;tablet&#39; bus=&#39;usb&#39;/&gt;
    &lt;input type=&#39;mouse&#39; bus=&#39;ps2&#39;/&gt;
    &lt;graphics type=&#39;vnc&#39; port=&#39;17237&#39; autoport=&#39;no&#39;/&gt;
  &lt;/devices&gt;
&lt;/domain&gt;

Domain maxMen allowed: 147456
Domain memory: 139140
Domain state: VIR_DOMAIN_BLOCKED
Domain network autostart: false
cpu: 31
cpu time: 2225977676675
cpu number: 0
cpu state: VIR_VCPU_BLOCKED
Domain network autostart: false
Inactive domain name: i-46A70811
Inactive domain name: i-38C20705
Inactive domain name: i-498E09B2
Inactive domain name: null
Inactive domain name: null
Inactive domain name: null
Inactive domain name: null
Inactive domain name: null
</pre>
<br />
结果分析：<br />
结果中基本包含了一个虚拟机组成的全部元素信息。如果你想做一个监控系统，你可以发现这里有
<blockquote>
	<p>
		<br />
		虚拟机的名字<br />
		虚拟机的Id<br />
		虚拟机的内存大小<br />
		虚拟CPU个数<br />
		虚拟机磁盘文件信息，磁盘文件的大小。甚至包括log等信息。<br />
		虚拟磁盘读写速率。<br />
		虚拟机网络设备信息。Mac地址，设备类型等。<br />
		虚拟机网卡读写速率。</p>
</blockquote>
基本可以满足一个监控系统的需求。<br />
说明一下上面的测试代码。libvirt Java API的入口基本都是通过Connect这个类，也就是首先建立与被管理主机之间的连接<br />
Connect kvmConn = new Connect(&quot;qemu+tcp://10.4.54.10/system&quot;);<br />
然后通过该连接获取信息：<br />
conn.listDomains()<br />
一个接口的如果需要接受参数：<br />
conn.domainLookupByID(activeDomId)<br />
肯定可以从其他的接口返回中找到答案：<br />
for (int activeDomId : conn.listDomains())<br />
只是有的获取的直接，有可能需要解析xml格式的返回值来获取需要参数值。比如：disk的paht和interface的path。<br />
最后再简单介绍一下管控接口：<br />
<br />
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
  /**
	 * 测试虚拟机的简单操作
	 * 
	 * @author lihzh
	 * @date 2012-5-16 下午3:35:43
	 */
	@Test
	public void testControlVM() {
		try {
			Domain domain = kvmConn.domainLookupByID(8);
			System.out.println(&quot;Domain state: &quot; + domain.getInfo().state);
			domain.suspend();
			System.out.println(&quot;Domain state: &quot; + domain.getInfo().state);
			for (int i = 0; i &lt; 5; i++) {
				System.out.println(&quot;wait for: &quot; + (5 - i));
				Thread.sleep(1000);
			}
			System.out.println(&quot;Resume vm.&quot;);
			domain.resume();
			System.out.println(&quot;Domain state: &quot; + domain.getInfo().state);
		} catch (LibvirtException e) {
			e.printStackTrace();
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
	}
</pre>
<br />
该用例主要测试了虚拟机的挂起和恢复操作。这类操作是比较简单的（因为无需参数）。一个复杂系统肯定需要包括虚拟机创建等操作。libvirt主要通过xml表述来创建资源，首先需要生成被创建虚拟机的完整描述，然后传递给创建的方法即可。描述的格式？呵呵，自然是上面测试结果给出的数据了。有兴趣的，大家可以自己尝试一下。libvirt的文档，还不完善，不过对于创建这样重要的功能，还是给出了说明。大家也可以下载官方的手册作为参考。<br />
好了，相对于VMware、Xenserver等虚拟化平台的SDK，libvirt的Java API还是比较简单的，上手很快，结构很简单。当然，功能上可能还是有所欠缺，信息量上，没有其他的那么充足。基于XML的方式操作资源，减少了接口的个数，使调用更直接，但是对开发人员却增加了困难。不过仍不失为一个不错的虚拟机环境操作的API，尤其是针对KVM/XEN的环境来说，可谓不二的选择。
