---
layout: post
title: Eclipse4.2+Tomcat7+Maven3+Servlet3.0 J2EE工程配置说明
date: 2012-09-29 09:16
author: onecoder
comments: true
categories: [eclipse, logback, Maven, servlet3, Web开发]
---
<p>
	<a href="http://www.coderli.com">OneCoder</a>准备自己慢慢写点东西，把离散的知识点汇总一下。给出的版本没什么特别的含义，只是<a href="http://www.coderli.com">OneCoder</a>目前使用的环境而已。</p>
<div>
	<ul>
		<li>
			IDE:Eclipse4.2 JUNO。</li>
		<li>
			应用服务器：Tomcat7.0.30（Servlet3.0）</li>
		<li>
			项目构建工具： Maven 3.0.4</li>
		<li>
			JDK版本：1.7.07</li>
		<li>
			日志组件：logback最新版</li>
		<li>
			单元测试框架：Junit</li>
	</ul>
	<div>
		为什么都用最新版本？因为<a href="http://www.coderli.com">OneCoder</a>是个新版控呵呵。</div>
	<div>
		为了以后管理方便，先建一个全局的parent工程，统一控制jar包依赖的版本。然后新建一个Maven管理的Web工程，parent指定为<a href="https://code.google.com/p/onecoder/">onecoder-parent</a>工程。</div>
	<div>
		&nbsp;</div>
	<div>
		Parent pom.xml配置样例：(主要是版本变量定义，依赖包设置和编译级别设置)</div>
	<div>
		<pre class="brush:xml;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
&lt;project xmlns=&quot;http://maven.apache.org/POM/4.0.0&quot; xmlns:xsi=&quot;http://www.w3.org/2001/XMLSchema-instance&quot;
	xsi:schemaLocation=&quot;http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd&quot;&gt;
	&lt;modelVersion&gt;4.0.0&lt;/modelVersion&gt;
	&lt;groupId&gt;onecoder-parent&lt;/groupId&gt;
	&lt;artifactId&gt;onecoder-parent&lt;/artifactId&gt;
	&lt;version&gt;0.1&lt;/version&gt;
	&lt;packaging&gt;pom&lt;/packaging&gt;
	&lt;properties&gt;
		&lt;logback.version&gt;1.0.7&lt;/logback.version&gt;
		&lt;slf4j.version&gt;1.7.1&lt;/slf4j.version&gt;
		&lt;junit.version&gt;4.10&lt;/junit.version&gt;
	&lt;/properties&gt;
	&lt;dependencyManagement&gt;
		&lt;dependencies&gt;
			&lt;dependency&gt;
				&lt;groupId&gt;junit&lt;/groupId&gt;
				&lt;artifactId&gt;junit&lt;/artifactId&gt;
				&lt;version&gt;${junit.version}&lt;/version&gt;
				&lt;scope&gt;test&lt;/scope&gt;
			&lt;/dependency&gt;
			&lt;dependency&gt;
				&lt;groupId&gt;org.slf4j&lt;/groupId&gt;
				&lt;artifactId&gt;slf4j-api&lt;/artifactId&gt;
				&lt;version&gt;${slf4j.version}&lt;/version&gt;
			&lt;/dependency&gt;
			&lt;dependency&gt;
				&lt;groupId&gt;ch.qos.logback&lt;/groupId&gt;
				&lt;artifactId&gt;logback-core&lt;/artifactId&gt;
				&lt;version&gt;${logback.version}&lt;/version&gt;
			&lt;/dependency&gt;
			&lt;dependency&gt;
				&lt;groupId&gt;ch.qos.logback&lt;/groupId&gt;
				&lt;artifactId&gt;logback-classic&lt;/artifactId&gt;
				&lt;version&gt;${logback.version}&lt;/version&gt;
			&lt;/dependency&gt;
		&lt;/dependencies&gt;
	&lt;/dependencyManagement&gt;
	&lt;build&gt;
		&lt;pluginManagement&gt;
			&lt;plugins&gt;
				&lt;plugin&gt;
					&lt;groupId&gt;org.apache.maven.plugins&lt;/groupId&gt;
					&lt;artifactId&gt;maven-compiler-plugin&lt;/artifactId&gt;
					&lt;version&gt;2.3.2&lt;/version&gt;
					&lt;configuration&gt;
						&lt;verbose&gt;true&lt;/verbose&gt;
						&lt;fork&gt;true&lt;/fork&gt;
						&lt;executable&gt;${JAVA_HOME}/bin/javac&lt;/executable&gt;
						&lt;compilerVersion&gt;1.7&lt;/compilerVersion&gt;
						&lt;source&gt;1.7&lt;/source&gt;
						&lt;target&gt;1.7&lt;/target&gt;
					&lt;/configuration&gt;
				&lt;/plugin&gt;

				&lt;plugin&gt;
					&lt;groupId&gt;org.apache.maven.plugins&lt;/groupId&gt;
					&lt;artifactId&gt;maven-surefire-plugin&lt;/artifactId&gt;
					&lt;version&gt;2.7.2&lt;/version&gt;
				&lt;/plugin&gt;
			&lt;/plugins&gt;
		&lt;/pluginManagement&gt;
	&lt;/build&gt;
&lt;/project&gt;
</pre>
	</div>
</div>
<p>
	logback的简要配置：</p>
<pre class="brush:xml;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
&lt;configuration&gt;  
  &lt;appender name=&quot;console&quot; class=&quot;ch.qos.logback.core.ConsoleAppender&quot;&gt;  
    &lt;encoder  class=&quot;ch.qos.logback.classic.encoder.PatternLayoutEncoder&quot;&gt;  
      &lt;pattern&gt;%d{yyyy/MM/dd-HH:mm:ss} %level [%thread] %C - %msg%n&lt;/pattern&gt;  
    &lt;/encoder &gt;  
  &lt;/appender&gt;  
  
  &lt;root level=&quot;INFO&quot;&gt;  
    &lt;appender-ref ref=&quot;console&quot; /&gt;  
  &lt;/root&gt;  
&lt;/configuration&gt;  
</pre>
<p>
	扔到src/main/resources下。</p>
<p style="text-align: center; ">
	<img alt="" src="http://onecoder.qiniudn.com/8wuliao/CiG3ZN9z/9wt4N.jpg" /></p>
<p>
	&nbsp;</p>
<div>
	如果你此时不知所措，不放直接扔到Tomcat下启动一下，看报什么错，就知道缺什么了。解决错误的过程，可以学到很多东西。</div>
<div>
	&nbsp;</div>
<div>
	接下来应该写Servlet了。Servlet3.0的一大亮点就是支持注解配置。所以你会发现没有了web.xml配置文件。Servlet写起来也很简单。</div>
<div>
	<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
/**
 * Servlet3.0 Servlet使用样例
 * @author lihzh
 * @alia OneCoder
 * @blog http://www.coderli.com
 */
@WebServlet(name = &quot;FirstServlet&quot;, urlPatterns = { &quot;/firstservlet&quot;})
public class FirstServlet extends HttpServlet {

	private static final long serialVersionUID = 3038754482452604279L;
	private static final Logger log = LoggerFactory.getLogger(FirstServlet.class);

        @Override
	public void doGet(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {

		PrintWriter out = response.getWriter();
		log.info(&quot;This is log&quot;);
		out.println(&quot;hello,world...&quot;);
		out.close();
	}
}</pre>
</div>
<div>
	一个简单的Servlet就写好了。</div>
<div>
	运行一下？等等。我们知道一个web工程运行时依赖的jar包是需要放到WEB-INF/lib下的，这里明显看到lib下空的，并且我们的jar包是通过Maven管理的，难道要手动拷贝过去吗？<a href="http://www.coderli.com">OneCoder</a>以前还真干过这样的啥事，甚至还自己开发了脚本，做所谓的&ldquo;一键自动化&rdquo;工作。其实Eclipse里已经帮你做好了。在工程的Properties配置项里的Deployment Assembly配置里，配好Maven的依赖和部署即可，如图：</div>
<div style="text-align: center; ">
	<img alt="" src="http://onecoder.qiniudn.com/8wuliao/CiG3YOTp/e1swU.jpg" /></div>
<div style="text-align: center; ">
	&nbsp;</div>
<div>
	此时再运行一下，log正常输出，说明jar依赖过来了，一切都ok了。</div>
<div style="text-align: center; ">
	<img alt="" src="http://onecoder.qiniudn.com/8wuliao/CiG3ZnD4/ddymn.jpg" /></div>
<div>
	剩下的就是你想用什么框架就引入什么了。<a href="http://www.coderli.com">OneCoder</a>会一遍配置，一遍简要的说明一下。</div>

