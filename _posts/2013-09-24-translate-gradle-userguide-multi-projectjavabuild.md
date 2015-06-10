---
layout: post
title: 《Gradle user guide》翻译 — 7.3 多工程Java构建
date: 2013-09-24 22:38
author: onecoder
comments: true
categories: [Gradle, gradle, userguide, 翻译]
---
<p>
	<span id="cke_bm_85S" style="display: none;">&nbsp;</span><span style="font-size: 13px; font-family: 'Microsoft YaHei';">现在我们来看一个典型的多项目构建的例子。下面是工程的布局：</span><br />
	<br />
	<span style="font-size: 13px; font-family: 'Microsoft YaHei';">样例7.10 多项目构建 - 分层布局</span><br />
	<br />
	<span style="font-size: 13px; font-family: 'Microsoft YaHei';">构建结构</span></p>
<blockquote>
	<p>
		<span style="font-size: 13px; font-family: 'Microsoft YaHei';">multiproject/</span><br />
		<span style="font-size: 13px; font-family: 'Microsoft YaHei';">&nbsp; api/</span><br />
		<span style="font-size: 13px; font-family: 'Microsoft YaHei';">&nbsp; services/webservice/</span><br />
		<span style="font-size: 13px; font-family: 'Microsoft YaHei';">&nbsp; shared/</span><br />
		&nbsp;</p>
</blockquote>
<table cellpadding="0" cellspacing="0" style="width: 618px; border-style: solid; border-color: rgb(128, 128, 128);" width="618.0">
	<tbody>
		<tr>
			<td style="width: 612px; border-style: solid; border-color: rgb(128, 128, 128); padding: 2px;" valign="top">
				<span style="font-size: 13px; font-family: 'Microsoft YaHei';">注意：本样例的代码可以在Gradle完整安装包的samples/java/multiproject目录下找到</span></td>
		</tr>
	</tbody>
</table>
<p>
	<span style="font-size: 13px; font-family: 'Microsoft YaHei';">这里我们有三个工程。api工程生成用于Java客户端访问XML webservice服务的JAR包。webservice工程是一个web应用，用于返回XML信息。shared工程包含了api和webservice工程共同使用的代码。</span></p>
<h2>
	<span style="font-size: 13px; font-family: 'Microsoft YaHei';">7.3.1 定义一个多项目构建</span></h2>
<p>
	<span style="font-size: 13px; font-family: 'Microsoft YaHei';">为了定义一个多项目构建，你需要创建一个配置文件(settings file)。该配置文件存放在源码树的根目录，指定了哪些项目需要包含在构建任务之中。该配置文件必须命名位settings.gradle。在本例中，我们使用一个简单的层次布局。下面是相关的配置文件：</span><br />
	<br />
	<span style="font-size: 13px; font-family: 'Microsoft YaHei';">样例7.11 多项目构建 - settings.gradle 文件</span></p>
<pre class="brush:groovy;title:'setting.gradle';first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
include &quot;shared&quot;, &quot;api&quot;,&quot;services:webservice&quot;, &quot;services:shared&quot;
</pre>
<p>
	<span style="font-size: 13px; font-family: 'Microsoft YaHei';">更多关于配置文件的信息，可在第56章的多项目构建中找到</span></p>
<h2>
	<span style="font-size: 13px; font-family: 'Microsoft YaHei';">7.3.2 常用配置</span></h2>
<p>
	<span style="font-size: 13px; font-family: 'Microsoft YaHei';">对于大多数的多项目构建来说，有一些对所有项目都常用的配置。在我们的样例中，我们使用一种叫做配置注入的技术，将这些通用配置定义在根项目中。这里，根项目就像一个容易，子项目在这种情况下覆盖了该容器中的元素 &mdash; 并注入了特殊的配置。这样，我们可以轻松的给所有归档定义清单内容和一些通用的依赖:</span><br />
	<br />
	<span style="font-size: 13px; font-family: 'Microsoft YaHei';">样例7.12 多项目构建 - 通用配置</span></p>
<pre class="brush:groovy;title:'build.gradle';first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
subprojects {
    apply plugin: &#39;java&#39;
    apply plugin: &#39;eclipse-wtp&#39;
    repositories {
       mavenCentral()
}
    dependencies {
        testCompile &#39;junit:junit:4.11&#39;
}
    version = &#39;1.0&#39;
    jar {
        manifest.attributes provider: &#39;gradle&#39;
} }

</pre>
<p>
	<span style="font-size: 13px; font-family: 'Microsoft YaHei';">注意到，我们的样例对每个子项目都应用Java插件。这意味着我们在之前部分看过的任务和配置属性对每个子项目都是可用的。因此，你可以通过在项目根路径执行gradle build命令给所有的工程进行编译，测试并打成JAR包。</span></p>
<h2>
	<span style="font-size: 13px; font-family: 'Microsoft YaHei';">7.3.3 项目间依赖</span></h2>
<p>
	<span style="font-size: 13px; font-family: 'Microsoft YaHei';">你可以在同一个构建中增加项目间的依赖，例如，一个项目的JAR包被用于另一个项目的编译。在api项目的构建文件中，我们将增加一个对shared工程JAR包的依赖。由于这个依赖的存在，Gradle将会确保shared工程总是在api项目之前先进行构建。</span><br />
	<br />
	<span style="font-size: 13px; font-family: 'Microsoft YaHei';">样例7.13 多项目构建 - 项目间依赖</span><br />
	<br />
	<span style="font-size: 13px; font-family: 'Microsoft YaHei';">api/build.gradle</span></p>
<pre class="brush:groovy;title:'api/build.gradle';first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
dependencies {
    compile project(&#39;:shared&#39;)
}
</pre>
<p>
	<span style="font-size: 13px; font-family: 'Microsoft YaHei';">关于如何关闭该功能，可参见第56.7.1章节的&ldquo;关闭依赖项目的构建&rdquo;部分。</span></p>
<h2>
	<span style="font-size: 13px; font-family: 'Microsoft YaHei';">7.3.4 创建一个分支</span></h2>
<p>
	<span style="font-size: 13px; font-family: 'Microsoft YaHei';">我们可以增加一个分支，用于打包给客户端使用：</span><br />
	<span style="font-size: 13px; font-family: 'Microsoft YaHei';">样例7.14 多项目构建 &mdash; 分支文件</span><br />
	<br />
	<span style="font-size: 13px; font-family: 'Microsoft YaHei';">api/build.gradle</span></p>
<pre class="brush:groovy;title:'api/build.gradle';first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
task dist(type: Zip) {
    dependsOn spiJar
    from &#39;src/dist&#39;
    into(&#39;libs&#39;) {
        from spiJar.archivePath
        from configurations.runtime
    }
}
artifacts {
   archives dist
}
</pre>
<p>
	&nbsp;</p>

