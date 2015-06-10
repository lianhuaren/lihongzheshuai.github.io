---
layout: post
title: 本站主题更新，基于《低调与华丽》DIY记录
date: 2012-07-01 14:42
author: onecoder
comments: true
categories: [wordpress, 主题, 站点运营]
---
<span style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">首先，感谢淘宝听(</span><span style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">&nbsp;</span><a href="http://www.taobaoting.net/" style="cursor: pointer; font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">http://www.taobaoting.net/</a><span style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">&nbsp;</span><span style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">)的分享，这套主题我一直很喜欢是</span><a href="http://lusongsong.com/" style="cursor: pointer; font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">卢松松</a><span style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">大哥制作的《低调与华丽》，只是卢松松大哥是Z-blog的，淘宝听提供了wordpress版本，不过logo和一些链接，需要自己修改。</span><br />
<br />
<span style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">记录一下修改过程，方便以后查阅：</span>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	<ol>
		<li>
			修改logo，自己制作了blog-logo.png文件，修改了style.css中37行的图片代码：
			<pre class="brush:css;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
#blog_title h1 a{color:#fff;height:108px;width:200px;font-size:0;text-indent:-9999px;background:url(images/blog-logo.png) left top no-repeat;display:block;}。
</pre>
		</li>
		<li>
			<span style="text-align: -webkit-auto; ">修改了header.php中#35的，banner链接，原来指向淘宝听。&lt;a href=&quot;http://bbs.coderli.com&quot;...</span></li>
		<li>
			制作了banner(468*60)</li>
		<li>
			修改foot.php#6的版权年份 2010改为2012：
			<pre class="brush:php;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
&lt;div class=&quot;copy&quot;&gt;&lt;p&gt;&lt;?php _e(&#39;Copyright &amp;copy; &#39;, &#39;xiaohan&#39;);?&gt; 2012
</pre>
		</li>
		<li>
			修改functions.php#214&nbsp;add_copyright()代码，改为自己的qq交谈</li>
		<li>
			修改sidebar.php，去掉内置广告，和登录组件。</li>
		<li>
			修改index.php#20-22，关于分类标签的代码，增加浏览次数：
			<pre class="brush:php;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
&lt;span&gt;分类：&lt;?php the_category(&#39;,&#39;); ?&gt; | &lt;/span&gt;&lt;span&gt;标签：&lt;?php the_tags(&#39;&#39;, &#39;, &#39;, &#39;&#39;); ?&gt; | &lt;/span&gt;&lt;span&gt;浏览：&lt;?php the_views(); ?&gt; 
</pre>
		</li>
		<li>
			修改single.php#38-40, 同上功能。</li>
		<li>
			修改catagory.php#16-18, 同上功能。</li>
		<li>
			修改tag.php#10-13，同上功能。</li>
		<li>
			修改archive.php#39-41，同上功能。</li>
		<li>
			修改search.php#10-12，同上功能。</li>
		<li>
			修改sidebar.php#104-109，增加站点统计，固定侧边栏。
			<pre class="brush:php;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
&lt;div class=&quot;block&quot;&gt;&lt;h3&gt;站点统计&lt;/h3&gt;&lt;li&gt;文章数：&lt;?php $count_posts = wp_count_posts(); echo $published_posts = $count_posts-&gt;publish; ?&gt;&lt;/li&gt;&lt;li&gt;评论数：&lt;?php $total_comments = get_comment_count(); echo $total_comments[&#39;approved&#39;];?&gt; &lt;/li&gt;&lt;li&gt;访问量：&lt;?php get_totalviews(true, true, true); ?&gt; &lt;/li&gt;&lt;/div&gt;
</pre>
		</li>
		<li>
			修改foot.php，增加百度分享浮窗</li>
		<li>
			修改single.php#52，将原分享按钮，改为自定义的百度分享。</li>
		<li>
			修改style.css#173，将侧边栏标题字体从12改为14</li>
		<li>
			<span style="text-align: -webkit-auto; ">修改style.css#111，增加文章中列表样式，否则，在文章中使用编号无效</span>
			<pre class="brush:css;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
.post .con ul li {list-style:disc inside;}.post .con ol li {list-style:decimal inside;}
</pre>
		</li>
	</ol>
	<span style="text-align: -webkit-auto; ">本着分享精神，本人修改过的主题也提供</span><a href="http://115.com/file/c26ttki4#didiaoandhuali.zip" style="cursor: pointer; text-align: -webkit-auto; ">下载</a><span style="text-align: -webkit-auto; ">。欢迎试用。<br />
	<br />
	<a href="http://www.coderli.com/wp-content/uploads/2012/07/didiaoandhuali.zip">本站下载</a></span><br />
	<a href="http://115.com/file/c26ttki4#didiaoandhuali.zip">115下载</a><br />
	&nbsp;</div>

