---
layout: post
title: Struts2拦截器实现文件过滤
date: 2012-06-13 22:18
author: onecoder
comments: true
categories: [filter, Java, Struts, struts2]
---
<p>
	手动实现文件过滤的方式虽然简单，但毕竟需要书写大量的过滤代码，不利于程序的高层次解构，而且开发复杂。 Struts 2提供了一个文件上传的拦截器，通过配置该拦截器可以更轻松地实现文件过滤。Struts 2中文件上传的拦截器是fileUpload，为了让该拦截器起作用，只需要在该Action中配置该拦截器引用即可。 配置fileUpload拦截器时，可以为其指定两个参数。 &Oslash;&nbsp;&nbsp;<span style="font-family: Arial;">allowedTypes</span>：该参数指定允许上传的文件类型，多个文件类型之间以英文逗号（<span style="font-family: Arial;">,</span>）隔开。 &Oslash;&nbsp;&nbsp;<span style="font-family: Arial;">maximumSize</span>：该参数指定允许上传的文件大小，单位是字节。 通过配置fileUpload的拦截器，可以更轻松地实现文件过滤，当文件过滤失败后，系统自动转入input逻辑视图，因此必须为该Action配置名为input的逻辑视图。除此之外，还必须显式地为该Action配置defaultStack的拦截器引用。 通过拦截器来实现文件过滤的配置文件如下。&nbsp;</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
image/png ,image/gif,image/jpeg 2000 /uploadFiles /WEB-INF/content/succ.jsp /WEB-INF/content/upload.jsp /WEB-INF/content/{1}.jsp 
</pre>
<p>
	上面的拦截器过滤不仅过滤了文件的类型，也过滤了文件大小。上传文件的类型只能是图片文件，并且文件大小不能大于2000字节（当然，我们随时可以更改到更大）。如果我们上传文件的文件太大，系统将转入input逻辑视图，也就是/WEB-INF/content/upload.jsp页面。 注意：<span style="font-family: 'Times New Roman'; ">如果需要使用文件上传拦截器来过滤文件大小，或者过滤文件内容，则必须显示配置引用Struts默认的拦截器栈：defaultStack。而且fileUpload拦截器必须配置在defaultStack拦截器栈之前。</span></p>
<p>
	<cite>本文出自 &ldquo;<a href="http://javaligang.blog.51cto.com/">疯狂Java李刚</a>&rdquo; 博客，请务必保留此出处<a href="http://javaligang.blog.51cto.com/5026500/890915">http://javaligang.blog.51cto.com/5026500/890915</a></cite></p>

