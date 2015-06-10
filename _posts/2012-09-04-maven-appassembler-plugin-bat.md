---
layout: post
title: 利用Maven插件打包产生可运行bat文件
date: 2012-09-04 23:02
author: onecoder
comments: true
categories: [appassembler, Maven, Maven, plugin]
---
<p>
	其实是一个很简单的技巧，就是利用Maven的appassembler-maven-plugin插件，就可以实现自动打包可运行的脚本，还可以跨平台。（Windows/linux）</p>
<div>
	首先在pom.xml文件的build节点下配置插件：</div>
<div>
	<pre class="brush:xml;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
&lt;plugin&gt;
&nbsp;&nbsp;&nbsp;&nbsp;&lt;groupId&gt;org.codehaus.mojo&lt;/groupId&gt;
    &lt;artifactId&gt;appassembler-maven-plugin&lt;/artifactId&gt;
    &lt;version&gt;1.1.1&lt;/version&gt;
	&lt;configuration&gt;
	&lt;repositoryLayout&gt;flat&lt;/repositoryLayout&gt;
	&lt;repositoryName&gt;lib&lt;/repositoryName&gt;
	&lt;configurationSourceDirectory&gt;src/main/resources/conf&lt;/configurationSourceDirectory&gt;
	&lt;!-- Set the target configuration directory to be used in the bin scripts --&gt;
	&lt;configurationDirectory&gt;conf&lt;/configurationDirectory&gt;
	&lt;!-- Copy the contents from &quot;/src/main/config&quot; to the target configuration 
	directory in the assembled application --&gt;
	&lt;copyConfigurationDirectory&gt;true&lt;/copyConfigurationDirectory&gt;
	&lt;!-- Include the target configuration directory in the beginning of 
		the classpath declaration in the bin scripts --&gt;
	&lt;includeConfigurationDirectoryInClasspath&gt;true&lt;/includeConfigurationDirectoryInClasspath&gt;
	&lt;!-- prefix all bin files with &quot;mycompany&quot; --&gt;
	&lt;binPrefix&gt;startup&lt;/binPrefix&gt;
	&lt;!-- set alternative assemble directory --&gt;
	&lt;assembleDirectory&gt;${project.build.directory}/server&lt;/assembleDirectory&gt;
	&lt;!-- Extra JVM arguments that will be included in the bin scripts --&gt;
	&lt;extraJvmArguments&gt;-Xms768m -Xmx768m -XX:PermSize=128m
						-XX:MaxPermSize=256m -XX:NewSize=192m -XX:MaxNewSize=384m
	&lt;/extraJvmArguments&gt;
	&lt;!-- Generate bin scripts for windows and unix pr default --&gt;
	&lt;platforms&gt;
	&lt;platform&gt;windows&lt;/platform&gt;
	&lt;platform&gt;unix&lt;/platform&gt;
	&lt;/platforms&gt;
	&lt;programs&gt;
	&lt;program&gt;
	&lt;mainClass&gt;com.coderli.onecoder.server.HypervisorServer&lt;/mainClass&gt;
	&lt;name&gt;startup&lt;/name&gt;
	&lt;/program&gt;
	&lt;/programs&gt;
	&lt;/configuration&gt;
&lt;/plugin&gt;
</pre>
</div>
<p>
	然后选择你要编译的工程，右键-&gt;maven build... 命令如下图：</p>
<p style="text-align: center; ">
	<img alt="" src="http://onecoder.qiniudn.com/8wuliao/Cf41VrLm/ogPHi.jpg" style="height: 478px; width: 600px; " /></p>
<p>
	然后Run一下：</p>
<p style="text-align: center; ">
	<img alt="" src="http://onecoder.qiniudn.com/8wuliao/Cf41W0YZ/HaQ6M.jpg" /></p>
<p>
	一个可执行的脚本文件就生成好了。startup.bat是windows下的，startup.sh是linux下的。具体参数，可以参考我上面给出的配置，也可以自己研究一下插件的配置。</p>

