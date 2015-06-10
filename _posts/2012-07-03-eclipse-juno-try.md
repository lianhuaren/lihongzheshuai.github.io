---
layout: post
title: Eclipse4.2 Juno升级体验
date: 2012-07-03 20:33
author: onecoder
comments: true
categories: [eclipse, Eclipse, juno]
---
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	前日，惊闻Eclipse发布了4.2正式版JUNO，迫不及待的体验一下。现在Eclipse的社区已经直接提供4.2版本的下载了。<a href="http://www.eclipse.org/downloads/" style="cursor: pointer; ">http://www.eclipse.org/downloads/</a>。 OneCoder论坛也提供了直接的下载连接，给怕麻烦的朋友。帖子地址：</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	<div>
		<a href="http://bbs.coderli.com/forum.php?mod=viewthread&amp;tid=226&amp;fromuid=1" style="cursor: pointer; ">Eclipse 4.2 (Juno) 正式版发布</a></div>
	<div>
		&nbsp;</div>
</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	下载后，解压，将3.7配置的启动参数直接复制过来，</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	&nbsp;</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	<div>
		-vmargs</div>
	<div>
		-Dosgi.requiredJavaVersion=1.5</div>
	<div>
		<font color="#ff0000">-Dhelp.lucene.tokenizer=standard</font></div>
	<div>
		-XX:PermSize=384M</div>
	<div>
		-XX:MaxPermSize=384M</div>
	<div>
		-XX:+UseParallelGC</div>
	<div>
		-Xms768m</div>
	<div>
		-Xmx768m</div>
</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	&nbsp;</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	注：红色一行为4.2新增的默认配置，具体功能未查。</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	&nbsp;</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	启动，选择以前的workspace，貌似一切安好。测试以前改过快捷键，仍然有效：）<br />
	<br />
	<img alt="juno-主页面" src="http://www.coderli.com/wp-content/uploads/2012/07/workspace-main.jpg" style="width: 550px; height: 547px; " /><br />
	<br />
	<div style="text-align: -webkit-auto; ">
		界面的风格有了比较大的变化，不过笔者喜欢新鲜，可以接受。</div>
	<div style="text-align: -webkit-auto; ">
		&nbsp;</div>
	<div style="text-align: -webkit-auto; ">
		那个Quick Access的搜索条，堪称亮点，可以快速进入你想要的配置页面。比如，输入：workspace&nbsp;&nbsp;&nbsp;&nbsp;<br />
		<br />
		<img alt="" src="http://www.coderli.com/wp-content/uploads/2012/07/quicksearch.png" style="width: 550px; height: 334px; " /><br />
		<br />
		<span style="text-align: -webkit-auto; ">会自动过滤出如下列表，选择最后的：general/Startup and Shutdown，就可以直接进入配置页面<br />
		<br />
		<input alt="" src="http://www.coderli.com/wp-content/uploads/2012/07/search-result.jpg" style="width: 472px; height: 164px; " type="image" /></span><br />
		<br />
		<div>
			Eclipse4.2宣称，兼容以前的插件。所以笔者决定一试：</div>
		<div>
			&nbsp;</div>
		<div>
			<ul>
				<li>
					直接在Dropins里扔入了3.7使用的，clearcase、ecobertura、resourcebundle插件。</li>
			</ul>
		</div>
		<div>
			<div style="text-align: -webkit-auto; ">
				&nbsp;&nbsp;<font color="#ff0000">&nbsp; 一切正常。可用。</font></div>
			<div style="text-align: -webkit-auto; ">
				<ul>
					<li>
						导入之前备份的update site的书签，在线安装Findbug、Maven、SVN、Git、RegexUtil插件。</li>
				</ul>
				<div>
					<font color="#ff0000">&nbsp; &nbsp; 一切正常，可用。</font></div>
			</div>
			<div style="text-align: -webkit-auto; ">
				<ul>
					<li>
						JD插件的原来的官网不知道为什么不可用了。无法在线安装也找不到合适的下载。只能手动从原来的plugins目录和features目录中提取出相关文件，放到了dropins下。</li>
				</ul>
				<div>
					&nbsp; &nbsp;&nbsp;<font color="#ff0000">能正常识别，但是貌似不可能用，没有反编译。</font></div>
			</div>
			<div style="text-align: -webkit-auto; ">
				总体来说，笔者升级后，完全可以工作，暂时没有什么问题。决定开始使用4.2。：）</div>
		</div>
	</div>
</div>

