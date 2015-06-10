---
layout: post
title: log4j2 与 log4j使用时的几点小区别 - log4j2上手说明
date: 2013-12-08 17:44
author: onecoder
comments: true
categories: [Java, jog4j2.xml, log4j, log4j2]
---
<p>
	虽然log4j2 目前还是beta版，不过<a href="http://www.coderli.com">OneCoder</a>已经忍不住要尝试一下。跟使用log4j 比起来，上手上主要的区别有。<br />
	1、依赖的jar包。使用slf4j+log4j2 时，依赖的jar包如下：(gradle配置，Maven对照修改即可)</p>
<pre class="brush:groovy;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
dependencies{
compile(
&quot;org.apache.logging.log4j:log4j-api:$log4j_version&quot;,
&quot;org.apache.logging.log4j:log4j-core:$log4j_version&quot;,
&quot;org.apache.logging.log4j:log4j-slf4j-impl:$log4j_version&quot;
)

}
</pre>
<p>
	其中，log4j_version=2.0-beta9</p>
<p>
	<br />
	2、默认配置文件名字<br />
	默认搜索的配置文件名字变为log4j2或log4j-test开头的配置文件，这个变化，让<a href="http://www.coderli.com">OneCoder</a>吃了些苦头。没注意观察，还自以为配置文件还是log4j.xml，结果怎么都不管用。后来仔细阅读官方文档才发现问题。log4j2中，支持json和xml两个格式的配置文件，配置文件的搜索顺序为：</p>
<blockquote>
	<p>
		&nbsp; 1. Log4j will inspect the &quot;log4j.configurationFile&quot; system property and, if set, will attempt to load the configuration using the ConfigurationFactory that matches the file extension.<br />
		&nbsp; 2. If no system property is set the JSON ConfigurationFactory will look for log4j2-test.json or log4j2-test.jsn in the classpath.<br />
		&nbsp; 3. If no such file is found the XML ConfigurationFactory will look for log4j2-test.xml in the classpath.<br />
		&nbsp; 4. If a test file cannot be located the JSON ConfigurationFactory will look for log4j2.json or log4j2.jsn on the classpath.<br />
		&nbsp; 5. If a JSON file cannot be located the XML ConfigurationFactory will try to locate log4j2.xml on the classpath.<br />
		&nbsp; 6. If no configuration file could be located the DefaultConfiguration will be used. This will cause logging output to go to the console.</p>
</blockquote>
<p>
	环境变量log4j.configurationFile指定的文件-&gt;log4j2-test.json/log4j2-test.jsn -&gt; log4j2-test.xml -&gt; log4j2.json/log4j2.jsn -&gt; log4j2.xml - &gt; 默认配置DefaultConfiguration</p>
<p>
	3、配置文件格式</p>
<p>
	配置文件样例，跟1比起来有所简化。</p>
<pre class="brush:xml;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
&lt;?xml version=&quot;1.0&quot; encoding=&quot;UTF-8&quot;?&gt;
&lt;Configuration status=&quot;WARN&quot;&gt;
     &lt;Appenders&gt;
          &lt;Console name=&quot;Console&quot; target=&quot;SYSTEM_OUT&quot;&gt;
               &lt;PatternLayout pattern=&quot;%d{yyyy-MM-dd HH:mm:ss} [%t] %-5level %l - %msg%n&quot; /&gt;
          &lt;/Console&gt;
     &lt;/Appenders&gt;
     &lt;Loggers&gt;
          &lt;Root level=&quot;debug&quot;&gt;
               &lt;AppenderRef ref=&quot;Console&quot; /&gt;
          &lt;/Root&gt;
     &lt;/Loggers&gt;
&lt;/Configuration&gt;
</pre>
<p>
	其他的变化和优势，您可以在使用中慢慢体会了。</p>

