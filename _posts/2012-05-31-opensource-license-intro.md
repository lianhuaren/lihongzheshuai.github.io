---
layout: post
title: 开发人员应留意的开源软件许可证简介
date: 2012-05-31 12:10
author: onecoder
comments: true
categories: [GPL, Java, 协议, 开源, 开源项目]
---
作为一个Java开发人员，开发中总会依赖很多的项目(jar包)，一般来说这些项目大部分都是开源的，但是开源不等于随意使用甚至商用。开源软件都有着自己的许可证，不同的许可证自然约束也是不同的。稍不留神，可能会自讨苦吃。
<div>
	先引用百度百科的开源软件的定义：</div>
<div>
	<span style="color: #0000ff;"><a href="http://baike.baidu.com/view/9664.htm"><span style="color: #0000ff;">http://baike.baidu.com/view/9664.htm</span></a></span></div>
<div>
	&nbsp;</div>
<blockquote>
	<div>
		<strong><a href="http://baike.baidu.com/view/444964.htm" target="_blank">开源软件</a>定义Version 1.9</strong>
		<div>
			&nbsp;</div>
		开源不仅仅表示开放程序源代码。从发行角度定义的开源软件必须符合如下条件：
		<div data-layout="right">
			<a href="http://baike.baidu.com/albums/9664/9664/0/0.html#0$29752a9b625284d0c8eaf474" target="_blank" title="查看图片">&nbsp;&nbsp;</a></div>
		<h3>
			1．自由再发行</h3>
		许可证不能限制任何团体销售或赠送软件，软件可以是几个不同来源的程序集成后的软件发行版中的其中一个原件。许可证不能要求对这样的销售收取许可证费或其他费用。
		<h3>
			<a name="2_2"></a><a name="sub9664_2_2"></a>2．程序源代码</h3>
		程序必须包含源代码。必须允许发行版在包含编译形式的同时也包含程序源代码。当产品以某种形式发行时没有包含源代码，必须非常醒目的告知用户，如何通过Internet免费的下载源代码。源代码必须是以当程序员修改程序时优先选用的形式提供。故意地扰乱源代码是不允许的。以预处理程序或翻译器这样的中间 形式作为源代码也是不允许的。
		<h3>
			<a name="2_3"></a><a name="sub9664_2_3"></a>3．派生程序</h3>
		许可证必须允许更改或派生程序。必须允许这些程序按与初始软件相同的许可证发行。
		<div data-layout="right">
			<a href="http://baike.baidu.com/albums/9664/9664/0/0.html#0$dbf554ed321854b1b21cb176" target="_blank" title="查看图片">&nbsp;&nbsp;</a></div>
		<h3>
			4．作者源代码的完整性</h3>
		只有当许可证允许在程序开发阶段，为了调整程序的目的将&ldquo;修补文件&rdquo;的发行版与源代码一起发行时，许可证才能限制源代码以更改后的形式发行。许可证必须明确地允许按更改后的源代码所建立的程序发行。许可证可以要求派生的程序使用与初始软件不同的名称或版本号。
		<h3>
			<a name="2_5"></a><a name="sub9664_2_5"></a>5．无个人或团体歧视</h3>
		许可证不能都有针对任何个人或团体制在专门奋斗领域内的任何人使用该程序。例如不能限制程序应用于商业领域，或者应用于遗传研究。
		<h3>
			<a name="2_6"></a><a name="sub9664_2_6"></a>7．许可证发行</h3>
		伴随程序所具有权力必须适用于所有的程序分销商，而不需要这些团体之间再附加许可证签字盖章。开源领军人物(3张)
		<h3>
			<a name="2_7"></a><a name="sub9664_2_7"></a>8．许可证不能特制某个产品</h3>
		如果程序是某个特殊的软件发行版中的一部分，伴随该程序所具有的权力不能只以来于这一发行版。如果程序是从那一发行版中摘录出来的，使用或发行时用的都是那个程序的许可证，分销程序的所有团体都应拥有与初始软件版所允许的所有权力。
		<h3>
			<a name="2_8"></a><a name="sub9664_2_8"></a>9．许可证不能排斥其他软件</h3>
		许可证不能限制随该许可证软件一起发行的其他软件。例如，许可证不能要求所有与之一起发行的其他软件都是开源软件。
		<h3>
			<a name="2_9"></a><a name="sub9664_2_9"></a>10．许可证实例</h3>
		GNU GPL、BSD、X Consortiun和Artistic许可证都是我们认为符合开源软件定义的许可证。MPL也是一样。</div>
</blockquote>
上面的定义可能比较书面和晦涩。一般在产生法律纠纷的时候会有作用，我们主要关注的是<span style="color: #ff0000;">我们所使用项目的所遵循的许可证</span>协议。说白了就是我们的产品是否需要开源，能不能商用。概括如下： 按照使用条件的不同，开源软件许可证可以分为三类（严苛程度递减）
<ol>
	<li>
		使用该开源软件的代码再散布（redistribute）时，源码也必须以相同许可证公开。代表许可类型：GPL， AGPL。</li>
	<li>
		&nbsp;使用该开源软件的代码并且对开源代码有所修改后再散布时，源码必须以相同许可证公开。代表许可类型：LGPL， CPL，CDDL， CPL，MPL等。</li>
	<li>
		&nbsp;使用该开源软件的代码（包括修改）再散布（redistribute）时，没有特殊限制，只需要明记许可。代表许可类型：ASL， BSD，MIT等。</li>
</ol>
<!--more-->一般Java开源人员常用的<span style="color: #ff0000;">Spring，Apache-commons</span>系列的项目等，都是属于<span style="color: #ff0000;">ASL</span>协议的。所以我们可以随意使用，但是玩意你用到了什么项目是<span style="color: #ff0000;">GPL</span>协议的，那你就得小心考虑了：）笔者是吃过苦的：）。
<div>
	更多关于许可证的信息可访问网站：&nbsp;<span style="color: #0000ff;"><a href="http://www.opensource.org/licenses " target="_blank"><span style="color: #0000ff;">http://www.opensource.org/licenses&nbsp;</span></a></span>了解。</div>

