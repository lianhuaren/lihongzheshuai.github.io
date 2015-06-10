---
layout: post
title: Discuz! X2.5 连续点击表情代码修改
date: 2012-06-22 13:38
author: onecoder
comments: true
categories: [discuz, js, 日记, 站点运营, 表情]
---
<font face="Tahoma, Helvetica, SimSun, sans-serif" style="font-size: 14px; text-align: -webkit-auto; "><span style="line-height: 23px; ">事先声明，本人完全不会php。只是略懂html、js。这个功能是我老婆想要的，自然想尽办法也得实现。</span></font>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	&nbsp;</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	<font face="Tahoma, Helvetica, SimSun, sans-serif"><span style="line-height: 23px; ">通过Chrome定位js函数，代码搜索等方式，找到了discuz包下，static/js路径下的editor.js和seditor.js两个文件。</span></font></div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	<font face="Tahoma, Helvetica, SimSun, sans-serif"><span style="line-height: 23px; ">其中editor.js中的insertSmiley方式，是点击表情是onClick触发的方法，可以看到最后有个hideMenu方法。完全凭语义判断，是关闭菜单的方法，于是注释掉这个代码。再发帖的编辑器里验证，果然生效。</span></font></div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	&nbsp;</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	<font face="Tahoma, Helvetica, SimSun, sans-serif"><span style="line-height: 23px; ">至于快速发帖编辑器的菜单，笔者修改的是seditor中的seditor_insertunit方法。同样注释掉其中的hideMenu方法。同样生效。</span></font></div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	&nbsp;</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	<font face="Tahoma, Helvetica, SimSun, sans-serif"><span style="line-height: 23px; ">PS：笔者确实不知这么修改会不会有其他的影响。不过目前来看，好用，也没什么问题：）。</span></font></div>

