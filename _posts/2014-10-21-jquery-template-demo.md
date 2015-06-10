---
layout: post
title: jQuery Template 上手体验
date: 2014-10-21 16:50
author: onecoder
comments: true
categories: [jQuery]
---
项目里用到了jQuery template这个插件，对我来说也算新鲜，学习记录一下。
这里使用的jQuery Template插件是指：https://github.com/BorisMoore/jquery-tmpl
如果您使用过模版引擎，估计对此也不会陌生。这个插件的用法其实很简单，通过下面代码相信就可以明白用法：
<pre class="brush:xml">&lt;!DOCTYPE html&gt;
&lt;html&gt;
&lt;head&gt;
&lt;meta charset="UTF-8"&gt;
&lt;title&gt; jQuery-template-demo&lt;/ title&gt;
&lt;script type= "text/javascript" src= "../../js/jQuery/jquery-2.1.1.js"&gt;&lt;/script &gt;
&lt;script type= "text/javascript" src= "../../js/jQuery/jquery.tmpl.min.js" &gt;&lt;/script &gt;
&lt;script type= "text/javascript"&gt;
     var tempData = [{Name: "OneCoder",Blog: "http://www.coderli.com" }];
     
     $(document).ready(function(){$("#template").tmpl(tempData).appendTo( "#list")});
&lt;/script&gt;
&lt;script id= "template" type="text/x-jquery-tmpl" &gt;
&lt;li&gt;
&lt;b&gt;${Name}&lt;/b&gt; (${Blog})
&lt;/li&gt;
&lt;/script&gt;
&lt;/head&gt;
&lt;body&gt;
&lt;ul id="list"&gt;&lt;/ ul&gt;
&lt;/body&gt;
&lt;/html&gt;
</pre>
依赖jQuery，解析JSON格式数据，替换模版中对应名称的变量，然后写到指定的位置。当然，该插件还有更多的功能和用法，比如if判断等，这里不一一介绍。
不过，OneCoder确实体会到了有了该插件后的便利。在需要动态生成html页面的时候，不需要再在用js或者java拼凑html代码，不直观，出错还不容易调试。用这个模版就方便多了。OneCoder前端弱见识短，说错勿怪。
