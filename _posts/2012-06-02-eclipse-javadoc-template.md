---
layout: post
title: Eclipse使用技巧 - 自定义JavaDoc注解和代码模版，提升开发效率和规范性
date: 2012-06-02 21:46
author: onecoder
comments: true
categories: [eclipse, Eclipse, javadoc, 代码模版]
---
<br />
项目中对于注释和代码规范的要求往往是毕比较严格的，如果全靠手动完成效率低，还难以保证保证的规范。幸好Eclipse给我们提供了自定义代码模版的功能。
<div>
	&nbsp;</div>
<div>
	先说一下Java代码注释模版，它是指这里的配置：</div>
<div>
	&nbsp;</div>
<div>
	<a href="http://www.coderli.com/wp-content/uploads/2012/06/preferences.jpg"><img alt="preferences" class="alignnone size-full wp-image-269" height="473" src="http://www.coderli.com/wp-content/uploads/2012/06/preferences.jpg" title="preferences" width="700" /></a></div>
<div>
	&nbsp;</div>
<div>
	是不是跟你的不一样，多了@author和@date ？，恩，这是我自定义过的注释模版。效果是在给方法用/**注释内容*/，注释的时候，会生成如下形式的代码：</div>
<div>
	&nbsp;</div>
<div style="text-align: center; ">
	<a href="http://www.coderli.com/wp-content/uploads/2012/06/method-javadoc.jpg"><img alt="method-javadoc" class="alignnone size-full wp-image-270" height="218" src="http://www.coderli.com/wp-content/uploads/2012/06/method-javadoc.jpg" title="method-javadoc" width="345" /></a></div>
<div>
	&nbsp;</div>
<div>
	<div>
		${tags}是生成@param，@return这些结果。其余的应该不用我多说了。</div>
	<div>
		&nbsp;</div>
	<div>
		定义这样的模版很简单，在刚才的位置，点Edit，按照如下的输入即可：</div>
</div>
<div>
	&nbsp;</div>
<div>
	<a href="http://www.coderli.com/wp-content/uploads/2012/06/edit-template.jpg"><img alt="edit-template" class="alignnone size-full wp-image-272" height="372" src="http://www.coderli.com/wp-content/uploads/2012/06/edit-template.jpg" title="edit-template" width="676" /></a></div>
<div>
	&nbsp;</div>
<div>
	这就够了？当然不是，如果在之前的方法忘记了@date时间注释，要怎么补上？直接用@ + 代码辅助？是不是找不到@date 标签？呵呵，当然，这个@date其实是我自定义的。定义的位置在这里：</div>
<div>
	&nbsp;</div>
<div>
	<a href="http://www.coderli.com/wp-content/uploads/2012/06/templates.jpg"><img alt="" class="alignnone size-full wp-image-271" height="359" src="http://www.coderli.com/wp-content/uploads/2012/06/templates.jpg" title="templates" width="775" /></a></div>
<div>
	&nbsp;</div>
<div>
	赶紧New一个 @date标签吧。</div>
<div>
	&nbsp;</div>
<div>
	<a href="http://www.coderli.com/wp-content/uploads/2012/06/new-template.jpg"><img alt="new-template" class="alignnone size-full wp-image-274" height="368" src="http://www.coderli.com/wp-content/uploads/2012/06/new-template.jpg" title="new-template" width="673" /></a></div>
<div>
	&nbsp;</div>
<div>
	<div>
		注：Pattern中 @date字符为手动填写。后面两个变量为Eclipse内部提供的。</div>
	<div>
		&nbsp;</div>
	<div>
		保存看看效果？</div>
</div>
<div>
	&nbsp;</div>
<div>
	<a href="http://www.coderli.com/wp-content/uploads/2012/06/date-javadoc.png"><img alt="" class="alignnone size-full wp-image-273" height="464" src="http://www.coderli.com/wp-content/uploads/2012/06/date-javadoc.png" title="date-javadoc" width="653" /></a></div>
<div>
	&nbsp;</div>
<div>
	<div>
		@date 出现了吧。</div>
	<div>
		&nbsp;</div>
	<div>
		是不是还想扩展Insert Variable里的内容？笔者也研究了一番，找到了这个：</div>
	<div>
		<span style="color: #0000ff;"><a href="http://www.coderli.com/archives/eclipse-template-variable/"><span style="color: #0000ff;">《Eclipse使用技巧 &ndash; 自定义注释模板变量》</span></a></span></div>
	<div>
		<span style="color: #0000ff;"><a href=" http://www.coderli.com/archives/eclipse-template-variable/" title="Eclipse使用技巧 – 自定义注释模板变量">&nbsp;<span style="color: #0000ff;">http://www.coderli.com/archives/eclipse-template-variable/</span></a></span></div>
	<div>
		&nbsp;不过考虑到操作性价比，笔者并未尝试，有兴趣的朋友可以研究一下，欢迎交流。</div>
</div>

