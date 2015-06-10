---
layout: post
title: slf4j+logback配置方式和logback.groovy配置文件
date: 2012-05-30 19:43
author: onecoder
comments: true
categories: [logback, logback.groovy, slf4j]
---
最近看到slf4j+logback的日志方案，并且介绍说，与log4j出自同一作者且做了不少优化，所以决定从commons-logging+log4j切换过来。<br />
logback官网：（该作者即为log4j的作者） <span style="color: #0000ff;"><a href="http://logback.qos.ch/"><span style="color: #0000ff;">http://logback.qos.ch/</span></a></span><br />
切换方式非常简单，在原有基础上加入如下jar包即可。<br />
slf4j-api-1.6.2.jar<br />
jcl-over-slf4j-1.6.2.jar &nbsp;\\用于桥接commons-logging 到 slf4j，<br />
如果直接使用slf4j+logback的方案则无需此jar logback目前最新版是1.0.3，<br />
下载链接如下： <span style="color: #0000ff;"><a href="http://logback.qos.ch/dist/logback-1.0.3.zip" target="_blank" title="logback download"><span style="color: #0000ff;">http://logback.qos.ch/dist/logback-1.0.3.zip</span></a></span><br />
Maven依赖如下：<br />
<pre class="brush:xml;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
&lt;dependency&gt; 
    &lt;groupId&gt;org.slf4j&lt;/groupId&gt; 
    &lt;artifactId&gt;slf4j-api&lt;/artifactId&gt; 
    &lt;version&gt;1.6.2&lt;/version&gt; 
&lt;/dependency&gt; 
&lt;dependency&gt; 
    &lt;groupId&gt;org.slf4j&lt;/groupId&gt; 
    &lt;artifactId&gt;jcl-over-slf4j&lt;/artifactId&gt; 
    &lt;version&gt;1.6.2&lt;/version&gt; 
&lt;/dependency&gt; 
&lt;dependency&gt; 
    &lt;groupId&gt;ch.qos.logback&lt;/groupId&gt; 
    &lt;artifactId&gt;logback-core&lt;/artifactId&gt; 
    &lt;version&gt;0.9.29&lt;/version&gt; 
&lt;/dependency&gt; 
&lt;dependency&gt; 
    &lt;groupId&gt;ch.qos.logback&lt;/groupId&gt; 
    &lt;artifactId&gt;logback-classic&lt;/artifactId&gt; 
    &lt;version&gt;0.9.29&lt;/version&gt; 
&lt;/dependency&gt; 
</pre>
<br />
logback会依次读取以下类型配置文件：<span style="color: #ff0000;">logback.groovy,logback-test.xml,logback.xml</span>（如果均不存在会采用默认配置） logback.xml样例如下：<br />
<pre class="brush:xml;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
&lt;?xml version=&quot;1.0&quot; encoding=&quot;UTF-8&quot;?&gt;   
&lt;configuration&gt;   
  &lt;appender name=&quot;stdout&quot; class=&quot;ch.qos.logback.core.ConsoleAppender&quot;&gt;   
    &lt;encoder  class=&quot;ch.qos.logback.classic.encoder.PatternLayoutEncoder&quot;&gt;   
      &lt;pattern&gt;%d{yyyy/MM/dd-HH:mm:ss.SSS} %level [%thread] %class:%line&gt;&gt;%msg%n&lt;/pattern&gt;   
    &lt;/encoder &gt;   
  &lt;/appender&gt;   
   
  &lt;root level=&quot;INFO&quot;&gt;   
    &lt;appender-ref ref=&quot;stdout&quot; /&gt;   
  &lt;/root&gt;   
&lt;/configuration&gt;
</pre>
其中pattern属性的意义跟log4j基本相同，具体可参考官方文档：<span style="color: #0000ff;"><a href="http://logback.qos.ch/manual/layouts.html"><span style="color: #0000ff;">http://logback.qos.ch/manual/layouts.html</span></a></span><br />
logback.groovy的样例代码如下：<br />
<pre class="brush:groovy;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
import static ch.qos.logback.classic.Level.DEBUG 
import ch.qos.logback.classic.encoder.PatternLayoutEncoder 
import ch.qos.logback.core.ConsoleAppender 
 
appender(&quot;CONSOLE&quot;, ConsoleAppender) { 
  encoder(PatternLayoutEncoder) { 
    pattern = &quot;%d{yyyy/MM/dd-HH:mm:ss} %-5level [%thread] %class{5}:%line&gt;&gt;%msg%n&quot; 
  } 
} 
root(DEBUG, [&quot;CONSOLE&quot;]) 
</pre>
官方提供了logback.xml-&gt;logback.groovy的转换工具，地址如下：<a href="http://logback.qos.ch/translator/asGroovy.html" style="padding: 0px; margin: 0px; color: rgb(125, 0, 0); text-decoration: none; font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: 14px; line-height: 25px; text-align: left; ">http://logback.qos.ch/translator/asGroovy.html</a>&nbsp;<br />
对于logback.groovy的使用，<span style="color:#ff0000;">需要注意的</span>是： logback.groovy需要放在源码包的根目录下，否则会找不到。 在eclipse中，如果安装了groovy的插件，需要将放置logback.groovy的源码包位置设置为groovysrcipt的所在位置，即在编译的时候不将goorvy编译成class文件，而是直接将groovy脚本复制到output path下。否则仍无法生效。 <a href="http://www.coderli.com/wp-content/uploads/2012/05/groovy-eclipse.jpg"><img alt="Groovy eclipse plugin config" class="alignnone size-full wp-image-165" height="217" src="http://www.coderli.com/wp-content/uploads/2012/05/groovy-eclipse.jpg" title="groovy-eclipse" width="654" /></a>
