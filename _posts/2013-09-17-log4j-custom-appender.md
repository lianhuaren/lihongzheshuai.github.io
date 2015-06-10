---
layout: post
title: 自定义实现log4j的appender
date: 2013-09-17 20:17
author: onecoder
comments: true
categories: [appender, Java, log4j, log4j]
---
<div id="xunlei_com_thunder_helper_plugin_d462f475-c18e-46be-bd10-327458d045bd">
	<span style="font: 14.0px Arial">log4j，应用最广泛的日志框架。其作者后来推出logback，也是好选择。不多说废话。<br />
	<br />
	log4j组件介绍<br />
	Log4j主要有三个组件：<br />
	&nbsp;&nbsp;&nbsp; Logger：负责供客户端代码调用，执行debug(Object msg)、info(Object msg)、warn(Object msg)、error(Object msg)等方法。<br />
	&nbsp;&nbsp;&nbsp; Appender：负责日志的输出，Log4j已经实现了多种不同目标的输出方式，可以向文件输出日志、向控制台输出日志、向Socket输出日志等。<br />
	&nbsp;&nbsp;&nbsp; Layout：负责日志信息的格式化。 </span>
	<p>
		<span style="font: 14.0px Arial">log4j默认提供了很多Appender，如org.apache.log4j.ConsoleAppender，FileAppender等。不过，如果有特殊的需求，就需要自定义实现，比如把日志发送到Flume中。我们的需求正是如此。<br />
		自定义一个Appender很简单，只需继承AppenderSkeleton类，并实现几个方法即可。</span></p>
</div>
<div>
	<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">

@Override
     protected void append(LoggingEvent event) {
         System.out.println(&quot;OneCoder: &quot; + event.getMessage());
     }


     @Override
     public void close() {
           // TODO Auto-generated method stub
     }


     @Override
     public boolean requiresLayout() {
           // TODO Auto-generated method stub
           return false ;
     }


</pre>
</div>
<div>
	上述代码的效果就是将原信息再开头加上&quot;OneCoder&quot;。我们想要发送到Flume里，也只需在该方法里添加Flume客户端的相关实现即可。这样你的系统日志就灵活多了。<br />
	当然，想要生效你需要修改log4j的配置文件，将appender改为你自己实现的appender即可。</div>
<div style="margin-left: 40px;">
	&nbsp;</div>
<div id="xunlei_com_thunder_helper_plugin_d462f475-c18e-46be-bd10-327458d045bd">
	&nbsp;</div>

