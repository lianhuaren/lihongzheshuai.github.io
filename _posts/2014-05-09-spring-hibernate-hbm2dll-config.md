---
layout: post
title: Spring集成Hibernate注解配置 无hibernate.cfg.xml文件，自动生成表配置
date: 2014-05-09 13:59
author: onecoder
comments: true
categories: [hbm2ddl, Hibernate, hibernate, Spring]
---
<p>
	本以为一个无足挂齿的小问题，没想到还折腾了一下。遂记录一下。主要搜索出的结果排名靠前的大多是在hibernate.cfg.xml中的配置方式。与我的环境不符。正确配置方式如下。已测试。</p>
<pre class="brush:xml;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
&lt;bean id= &quot;sessionFactory&quot;
           class= &quot;org.springframework.orm.hibernate3.annotation.AnnotationSessionFactoryBean&quot; &gt;
           &lt;property name =&quot;dataSource&quot;&gt;
               &lt;ref bean =&quot;dataSource&quot; /&gt;
           &lt;/property &gt;
           &lt;property name =&quot;packagesToScan&quot; value= &quot;xxx.xxx&quot; /&gt;
           &lt;property name =&quot;hibernateProperties&quot;&gt;
               &lt;props &gt;
                    &lt;prop key= &quot;hibernate.hbm2ddl.auto&quot;&gt;create&lt;/prop &gt;
               &lt;/props &gt;
               &lt;!-- &lt;value&gt;hibernate.hbm2ddl.auto=create&lt;/value&gt; --&gt;
           &lt;/property &gt;
     &lt;/bean &gt;
</pre>
<p>
	注：注释掉的配置value配置方式和prop配置方式，均有效。<br />
	&nbsp;</p>

