---
layout: post
title: Flume(ng) 自定义sink实现和属性注入
date: 2013-10-29 11:14
author: onecoder
comments: true
categories: [flume, flume, flume-ng, log4j, sink]
---
<div>
	<div>
		最近需要利用flume来做收集远端日志，所以学习一些flume最基本的用法。这里仅作记录。</div>
	<div>
		&nbsp;</div>
	<div>
		远端日志收集的整体思路是远端自定义实现log4j的appender把消息发送到flume端，flume端自定义实现一个sink来按照我们的规则保存日志。</div>
	<div>
		&nbsp;</div>
	<div>
		自定义Sink代码：</div>
	<br />
	<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
public class LocalFileLogSink extends AbstractSink implements Configurable {
     private static final Logger logger = LoggerFactory
              . getLogger(LocalFileLogSink .class );
            private static final String PROP_KEY_ROOTPATH = &quot;rootPath&quot;;
      private String rootPath;
     @Override
     public void configure(Context context) {
          String rootPath = context.getString(PROP_KEY_ROOTPATH );
          setRootPath(rootPath);
     }
          
          @Override
          public Status process() throws EventDeliveryException {
           logger .debug(&quot;Do process&quot; );
       ｝
}
</pre>
	<div style="color: rgb(0, 0, 0); font-family: 微软雅黑; font-style: normal; font-variant: normal; font-weight: normal; letter-spacing: normal; line-height: normal; orphans: 2; text-align: -webkit-auto; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-size-adjust: auto; -webkit-text-stroke-width: 0px; font-size: medium;">
		&nbsp;</div>
	<div style="color: rgb(0, 0, 0); font-family: 微软雅黑; font-style: normal; font-variant: normal; font-weight: normal; letter-spacing: normal; line-height: normal; orphans: 2; text-align: -webkit-auto; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-size-adjust: auto; -webkit-text-stroke-width: 0px; font-size: medium;">
		实现Configurable接口，即可在初始化时，通过configure方法从context中获取配置的参数的值。这里，我们是想从flume的配置文件中获取rootPath的值，也就是日志保存的根路径。在flume-conf.properties中配置如下：</div>
	<div style="color: rgb(0, 0, 0); font-family: 微软雅黑; font-style: normal; font-variant: normal; font-weight: normal; letter-spacing: normal; line-height: normal; orphans: 2; text-align: -webkit-auto; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-size-adjust: auto; -webkit-text-stroke-width: 0px; font-size: medium;">
		<pre class="brush:plain;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
agent.sinks = loggerSink
agent.sinks.loggerSink.rootPath = ./logs
</pre>
	</div>
	<div style="color: rgb(0, 0, 0); font-family: 微软雅黑; font-style: normal; font-variant: normal; font-weight: normal; letter-spacing: normal; line-height: normal; orphans: 2; text-align: -webkit-auto; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-size-adjust: auto; -webkit-text-stroke-width: 0px; font-size: medium;">
		loggerSink是自定义sink的名称，我们取值时的key，只需要loggerSink后面的部分即可，即这里的rootPath。</div>
	<div style="color: rgb(0, 0, 0); font-family: 微软雅黑; font-style: normal; font-variant: normal; font-weight: normal; letter-spacing: normal; line-height: normal; orphans: 2; text-align: -webkit-auto; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-size-adjust: auto; -webkit-text-stroke-width: 0px; font-size: medium;">
		&nbsp;</div>
	<div style="color: rgb(0, 0, 0); font-family: 微软雅黑; font-style: normal; font-variant: normal; font-weight: normal; letter-spacing: normal; line-height: normal; orphans: 2; text-align: -webkit-auto; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-size-adjust: auto; -webkit-text-stroke-width: 0px; font-size: medium;">
		实际业务逻辑的执行，是通过继承复写AbstractSink中的process方法实现。从基类的getChannel方法中获取信道，从中取出Event处理即可。</div>
	<br />
	<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
 Channel ch = getChannel();
            Transaction txn = ch.getTransaction();
          txn.begin();
           try {
               logger .debug(&quot;Get event.&quot; );
              Event event = ch.take();
              txn.commit();
              status = Status. READY ;
              return status;
                    ｝finally {
              Log. info( &quot;trx close.&quot;);
              txn.close();
          }
</pre>
</div>
<p>
	&nbsp;</p>

