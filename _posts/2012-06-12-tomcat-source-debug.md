---
layout: post
title: Tomcat7调试运行环境搭建与源代码分析入门
date: 2012-06-12 20:19
author: onecoder
comments: true
categories: [eclipse, Tomcat, tomcat, 源码]
---
<h3>
	<strong><span style="color: #000000;">1. 需要准备好下面这些工具</span></strong></h3>
<p>
	JDK 1.6+ Maven 2或3 TortoiseSVN 1.7+ (从1.7开始&rdquo;.svn&rdquo;目录集中放在一处了，不再每个目录下都放一份) Eclipse 3.5+ 这4个工具不在这里描述怎么配置了，如果你是有两三年开发经验的Java开发人员，正常来讲都一直在用了。 另外，分析tomcat源代码不需要对这4个工具做什么特殊配置。</p>
<h3>
	<strong><span style="color: #000000;">2. 下载Tomcat的源代码</span></strong></h3>
<p>
	Apache旗下的开源项目基本上都放在这:&nbsp;<a href="http://svn.apache.org/repos/asf" title="http://svn.apache.org/repos/asf">http://svn.apache.org/repos/asf</a> 里面包含了tomcat、struts、hadoop、hbase等流行的开源项目的源代码， 可以直接用浏览器打开这个URL，或者用TortoiseSVN的Repository Browser打开它。 tomcat的svn是:&nbsp;<a href="http://svn.apache.org/repos/asf/tomcat" title="http://svn.apache.org/repos/asf/tomcat">http://svn.apache.org/repos/asf/tomcat</a>, 如下图所示: <a href="http://rdc.taobao.com/team/jm/files/2012/06/12.jpg"><img alt="" height="487" src="http://rdc.taobao.com/team/jm/files/2012/06/12.jpg" width="740" /></a> 目前tomcat有4个大分支: 5.5 :&nbsp;<a href="http://svn.apache.org/repos/asf/tomcat/tc5.5.x" title="http://svn.apache.org/repos/asf/tomcat/tc5.5.x">http://svn.apache.org/repos/asf/tomcat/tc5.5.x</a> 6.0 :&nbsp;<a href="http://svn.apache.org/repos/asf/tomcat/tc6.0.x" title="http://svn.apache.org/repos/asf/tomcat/tc6.0.x">http://svn.apache.org/repos/asf/tomcat/tc6.0.x</a> 7.0 :&nbsp;<a href="http://svn.apache.org/repos/asf/tomcat/tc7.0.x" title="http://svn.apache.org/repos/asf/tomcat/tc7.0.x">http://svn.apache.org/repos/asf/tomcat/tc7.0.x</a> 8.0 :&nbsp;<a href="http://svn.apache.org/repos/asf/tomcat/trunk" title="http://svn.apache.org/repos/asf/tomcat/trunk">http://svn.apache.org/repos/asf/tomcat/trunk</a> 5.5分支会在今年9月30号后停止维护，所以除非有历史遗留系统，不推荐再去读它的代码， 6.0分支是比较成熟的，在生产环境用得比较多， 目前官方对这个分支进入维护、bugfix阶段，很少有新功能添加进来了， 我个人也不推荐读它的代码，代码相对7.0来讲比较脏乱。 7.0分支完整实现了servlet 3.0规范，已陆续发布了27个小版本，己经稳定了，可用于生产环境， 代码比5.5、6.0分支干净整洁得多，这也是我强烈向你推荐的版本。 8.0分支主要关注web socket和spdy，正处于活跃开发阶段，代码变动比较频繁，保持关注即可。 所以这篇文章讲的是7.0分支，研究tomcat推荐直接提取svn的源代码: 用TortoiseSVN checkout这个svn的代码:&nbsp;<a href="http://svn.apache.org/repos/asf/tomcat/tc7.0.x/trunk" title="http://svn.apache.org/repos/asf/tomcat/tc7.0.x/trunk">http://svn.apache.org/repos/asf/tomcat/tc7.0.x/trunk</a> 放到D:\Tomcat7\trunk (你可以换别的目录) 然后再从这下载一个二进制分发包(Binary Distributions) <a href="http://labs.mop.com/apache-mirror/tomcat/tomcat-7/v7.0.27/bin/apache-tomcat-7.0.27.zip" title="http://labs.mop.com/apache-mirror/tomcat/tomcat-7/v7.0.27/bin/apache-tomcat-7.0.27.zip">http://labs.mop.com/apache-mirror/tomcat/tomcat-7/v7.0.27/bin/apache-tomcat-7.0.27.zip</a> 解压后放到D:\Tomcat7，顺便把&rdquo;apache-tomcat-7.0.27&Prime;重命名成launch吧， 用这个二进制分发包而不是从源代码构建只是为了节省时间， 直接用它conf目录里面的配置文件和webapps下的例子。</p>
<h3>
	<strong><span style="color: #000000;">3. 把它变成maven工程</span></strong></h3>
<p>
	主要是添加几个依赖(ecj、ant、jaxrpc等)，否则的话导入eclipse后会有编译错误， 另外，因为tomcat不是标准的maven工程项目，比如没有src\main\java这样的目录， 所以要调整一下sourceDirectory和testSourceDirectory，下面是一个完整的pom文件， 直接放到D:\Tomcat7目录即可(pom.xml与之前的launch、trunk目录并列)</p>
<p>
	&nbsp;</p>
<pre class="brush:xml;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
&lt;project xmlns=&ldquo;http://maven.apache.org/POM/4.0.0&Prime; xmlns:xsi=&ldquo;http://www.w3.org/2001/XMLSchema-instance&rdquo;  
    xsi:schemaLocation=&ldquo;http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd&rdquo;&gt;  
    &lt;modelVersion&gt;4.0.0&lt;/modelVersion&gt;  
  
    &lt;groupId&gt;org.apache.tomcat&lt;/groupId&gt;  
    &lt;artifactId&gt;Tomcat7.0&lt;/artifactId&gt;  
    &lt;name&gt;Tomcat7.0&lt;/name&gt;  
    &lt;version&gt;7.0&lt;/version&gt;  
  
    &lt;build&gt;  
        &lt;finalName&gt;Tomcat7.0&lt;/finalName&gt;  
        &lt;sourceDirectory&gt;trunk/java&lt;/sourceDirectory&gt;  
        &lt;testSourceDirectory&gt;trunk/test&lt;/testSourceDirectory&gt;  
        &lt;resources&gt;  
            &lt;resource&gt;  
                &lt;directory&gt;trunk/java&lt;/directory&gt;  
            &lt;/resource&gt;  
        &lt;/resources&gt;  
        &lt;testResources&gt;  
            &lt;testResource&gt;  
                &lt;directory&gt;trunk/test&lt;/directory&gt;  
            &lt;/testResource&gt;  
        &lt;/testResources&gt;  
        &lt;plugins&gt;  
            &lt;plugin&gt;  
                &lt;groupId&gt;org.apache.maven.plugins&lt;/groupId&gt;  
                &lt;artifactId&gt;maven-compiler-plugin&lt;/artifactId&gt;  
                &lt;version&gt;2.3&lt;/version&gt;  
                &lt;configuration&gt;  
                    &lt;source&gt;1.6&lt;/source&gt;  
                    &lt;target&gt;1.6&lt;/target&gt;  
                &lt;/configuration&gt;  
            &lt;/plugin&gt;  
        &lt;/plugins&gt;  
    &lt;/build&gt;  
  
    &lt;dependencies&gt;  
        &lt;dependency&gt;  
            &lt;groupId&gt;junit&lt;/groupId&gt;  
            &lt;artifactId&gt;junit&lt;/artifactId&gt;  
            &lt;version&gt;4.4&lt;/version&gt;  
            &lt;scope&gt;test&lt;/scope&gt;  
        &lt;/dependency&gt;  
        &lt;dependency&gt;  
            &lt;groupId&gt;org.eclipse.jdt.core.compiler&lt;/groupId&gt;  
            &lt;artifactId&gt;ecj&lt;/artifactId&gt;  
            &lt;version&gt;3.7.2&lt;/version&gt;  
        &lt;/dependency&gt;  
        &lt;dependency&gt;  
            &lt;groupId&gt;ant&lt;/groupId&gt;  
            &lt;artifactId&gt;ant&lt;/artifactId&gt;  
            &lt;version&gt;1.7.0&lt;/version&gt;  
        &lt;/dependency&gt;  
        &lt;dependency&gt;  
            &lt;groupId&gt;wsdl4j&lt;/groupId&gt;  
            &lt;artifactId&gt;wsdl4j&lt;/artifactId&gt;  
            &lt;version&gt;1.6.2&lt;/version&gt;  
        &lt;/dependency&gt;  
        &lt;dependency&gt;  
            &lt;groupId&gt;javax.xml&lt;/groupId&gt;  
            &lt;artifactId&gt;jaxrpc&lt;/artifactId&gt;  
            &lt;version&gt;1.1&lt;/version&gt;  
        &lt;/dependency&gt;  
    &lt;/dependencies&gt;  
  
&lt;/project&gt; 
</pre>
<p>
	&nbsp;</p>
<p>
	<source></source></p>
<source><source><source><source>
<p>
	<source> </source></p>
<source><source><source><source>
<h3>
	<span style="font-size: medium;"><strong><span style="color: #000000;">4. 导入Eclipse</span></strong></span></h3>
<span style="font-size: medium;">在命令行窗口中进入D:\Tomcat7目录，执行 mvn eclipse:eclipse 就可以转成eclipse工程项目了， 然后打开eclipse，点&rdquo;File-&gt;Import-&gt;General-&gt;Existing Projects into Workspace&rdquo;， 最后打开D:\Tomcat7就能看到Tomcat7.0这个项目了。 (如果eclipse装了m2e插件不用执行mvn eclipse:eclipse的，可以直接导入maven工程) </span>
<h3>
	<span style="font-size: medium;"><strong><span style="color: #000000;">5. 在Eclipse中让Tomcat跑起来</span></strong></span></h3>
<p>
	<span style="font-size: medium;">在Eclipse中打开org.apache.catalina.startup.Bootstrap类， 在编辑区右击，点&rdquo;Run As-&gt;Run configurations&rdquo;，然后双击&rdquo;Java Aplication&rdquo;就会出来一个新的&rdquo;Bootstrap&rdquo;， 选中它，在右边点击&rdquo;Arguments&rdquo;那一栏，把下面的内容copy到&rdquo;VM arguments&rdquo;中:</span></p>
<p>
	<span style="color: rgb(0, 128, 0); font-family: Tahoma, Arial, Helvetica, sans-serif; font-size: 13px; line-height: 18px; text-align: left; background-color: rgb(247, 247, 247); ">-Dcatalina.home=launch -Dcatalina.base=launch -Djava.endorsed.dirs=launch/endorsed -Djava.io.tmpdir=launch/temp -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Djava.util.logging.config.file=launch/conf/logging.properties</span></p>
<p>
	<span style="font-size: medium;">如下图:</span></p>
<p>
	<span style="font-size: medium;"><a href="http://rdc.taobao.com/team/jm/files/2012/06/2.jpg"><img alt="" height="448" src="http://rdc.taobao.com/team/jm/files/2012/06/2.jpg" width="568" /></a> </span></p>
<p>
	<span style="font-size: medium;">然后点run按钮，就可以启动tomcat了，启动成功会在Eclipse的console中显示: </span></p>
<p>
	&nbsp;</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
2012-6-10 14:25:31 org.apache.catalina.startup.Catalina start  
信息: Server startup in 359 ms  
</pre>
<p>
	<span style="font-size: medium;">&nbsp;除上面这种方式外，这里还有一个Eclipse的launch脚本(start-tomcat7.launch&nbsp;(注: 在附件中))，&nbsp;</span>&lt;?xml version=&ldquo;1.0&Prime; encoding=&ldquo;UTF-8&Prime; standalone=&ldquo;no&rdquo;?&gt; &nbsp;</p>
<pre class="brush:xml;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
&lt;launchConfiguration type=&ldquo;org.eclipse.jdt.launching.localJavaApplication&rdquo;&gt;  
&lt;listAttribute key=&ldquo;org.eclipse.debug.core.MAPPED_RESOURCE_PATHS&rdquo;&gt;  
&lt;listEntry value=&ldquo;/Tomcat7.0/trunk/java/org/apache/catalina/startup/Bootstrap.java&rdquo;/&gt;  
&lt;/listAttribute&gt;  
&lt;listAttribute key=&ldquo;org.eclipse.debug.core.MAPPED_RESOURCE_TYPES&rdquo;&gt;  
&lt;listEntry value=&ldquo;1&Prime;/&gt;  
&lt;/listAttribute&gt;  
&lt;stringAttribute key=&ldquo;org.eclipse.jdt.launching.MAIN_TYPE&rdquo; value=&ldquo;org.apache.catalina.startup.Bootstrap&rdquo;/&gt;  
&lt;stringAttribute key=&ldquo;org.eclipse.jdt.launching.PROGRAM_ARGUMENTS&rdquo; value=&ldquo;start&rdquo;/&gt;  
&lt;stringAttribute key=&ldquo;org.eclipse.jdt.launching.PROJECT_ATTR&rdquo; value=&ldquo;Tomcat7.0&Prime;/&gt;  
&lt;stringAttribute key=&ldquo;org.eclipse.jdt.launching.VM_ARGUMENTS&rdquo; value=&ldquo;-Dcatalina.home=launch -Dcatalina.base=launch -Djava.endorsed.dirs=launch/endorsed -Djava.io.tmpdir=launch/temp -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Djava.util.logging.config.file=launch/conf/logging.properties&rdquo;/&gt;  
&lt;/launchConfiguration&gt;  
</pre>
<p>
	<span style="font-size: medium;">可以放到D:\Tomcat7目录，然后flush一下Eclipse，在Eclipse中右击这个文件，点Run As启动Tomcat，点Debug As可以调试Tomcat。 下图是Tomcat在Eclipse中的项目布局: </span></p>
<p>
	<span style="font-size: medium;"><a href="http://rdc.taobao.com/team/jm/files/2012/06/3.jpg"><img alt="" height="186" src="http://rdc.taobao.com/team/jm/files/2012/06/3.jpg" width="241" /></a> </span></p>
<p>
	<span style="font-size: medium;">最后，打开你的浏览器，输入&nbsp;<a href="http://127.0.0.1:8080/examples/" title="http://127.0.0.1:8080/examples/">http://127.0.0.1:8080/examples/</a>&nbsp;看看例子吧。 </span></p>
<h3>
	<span style="font-size: medium;"><strong><span style="color: #000000;">6. 简单的源代码阅读指南:</span></strong></span></h3>
<p>
	&nbsp;</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
包名                    用途  
=================================================  
javax                 servlet/jsp/el相关的api  
org.apache.catalina   tomcat自身架构  
org.apache.coyote     http、ajp协议实现相关的类  
org.apache.el         实现el规范  
org.apache.jasper     实现jsp规范、编译jsp文件  
org.apache.juli       tomcat的日志系统  
org.apache.naming     jndi实现  
org.apache.tomcat     tomcat的工具包、net、digester xml解析器 &nbsp;</pre>
<div>
	<p>
		阅读顺序:</p>
	<p>
		可以从org.apache.catalina.startup.Bootstrap这个类开始看起，</p>
	<p>
		然后到org.apache.catalina.startup.Catalina,</p>
	<p>
		在Catalina类中会触发conf/server.xml文件的解析，</p>
	<p>
		这时要看org.apache.tomcat.util.digester中的类，</p>
	<p>
		解析的过程中会用到org.apache.catalina.startup包中的很多RuleSet类，</p>
	<p>
		server.xml文件解析完后，会生成org.apache.catalina.core包中的各种StandardXXX类的实例，</p>
	<p>
		比如StandardServer、StandardService、StandardEngine等等，</p>
	<p>
		这些Standard组件都是有生命周期的，接着会调用他们的init、start等方法，</p>
	<p>
		会触发下面这些组件进入init、start状态</p>
	<p>
		org.apache.catalina.connector.Connector</p>
	<p>
		org.apache.coyote.http11.Http11Protocol</p>
	<p>
		org.apache.tomcat.util.net.JIoEndpoint</p>
	<p>
		在JIoEndpoint(或NioEndpoint、AprEndpoint)中会监听8080这样的端口，</p>
	<p>
		有请求进来了，就进行相关的io操作，接着转到org.apache.coyote包中的相应类进行协议解析，</p>
	<p>
		生成org.apache.catalina.connector.Request和org.apache.catalina.connector.Response实例，</p>
	<p>
		然后转到各种Valve、应用Filter，最后到达应用的Servlet/JSP。</p>
	<div>
		下图描述了Tomcat7的核心架构:&nbsp;<span style="font-size: medium;"><a href="http://rdc.taobao.com/team/jm/files/2012/06/4.png"><img alt="" height="500" src="http://rdc.taobao.com/team/jm/files/2012/06/4.png" width="600" /></a> <cite>转自：<a href="http://rdc.taobao.com/team/jm/archives/1809">http://rdc.taobao.com/team/jm/archives/1809</a></cite> </span></div>
</div>
<p>
	&nbsp;</p>
<p>
	&nbsp;</p>
</source></source></source></source></source></source></source></source>
