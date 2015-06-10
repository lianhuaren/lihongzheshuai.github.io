---
layout: post
title: wp-postviews双倍统计问题修正
date: 2012-08-12 15:37
author: onecoder
comments: true
categories: [wordpress, wp-postviews, 双倍统计, 站点运营]
---
<p>
	最近给老婆的休闲小站<a href="http://8wuliao.net/">8无聊</a>，更新了一套主题。加上常用的功能后，本以为万事大吉了。谁知没用今天，就收到老婆的反馈，说文章里的统计量和百度统计不匹配，比百度统计多。</p>
<div>
	<a href="http://www.coderli.com">OneCoder</a>回忆，之前的主题没有出现这个问题，那么这个问题应该是出在新换的主题上。不过，不管怎样，还是先上google搜索一番，不过说的最多的也就是所谓多了个前缀1的问题，不是<a href="http://www.coderli.com">OneCoder</a>想要的。遇到一个讨论双倍统计问题了，不过没有答案。但是里面提到了一句话，说看看是不是加载了两次，点了<a href="http://www.coderli.com">OneCoder</a>一下。于是，<a href="http://www.coderli.com">OneCoder</a>开始查看single.php页面。发现查看统计量的代码是这样的：</div>
<div>
	<pre class="brush:php;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
浏览：&lt;?php post_views(); ?&gt;
</pre>
</div>
<p>
	这引起了<a href="http://www.coderli.com">OneCoder</a>的注意，因为记得以前调用wp-postviews的统计代码的方式是：</p>
<pre class="brush:php;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
&lt;?php the_views(); ?&gt;
</pre>
<p>
	不可能是调用方式修改了，因为我压根就没更新过。笔者修改了一下，果然还能起作用。这让<a href="http://www.coderli.com">OneCoder</a>坚信，这个主题里肯定提供了内置的访问数统计和查询代码。于是，挨个页面查找。终于在funtions.php页面找到了如下代码：</p>
<pre class="brush:php;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
function record_visitors()
{
	if (is_singular()) 
	{
	  global $post;
	  $post_ID = $post-&gt;ID;
	  if($post_ID) 
	  {
		  $post_views = (int)get_post_meta($post_ID, &#39;views&#39;, true);
		  if(!update_post_meta($post_ID, &#39;views&#39;, ($post_views+1))) 
		  {
			add_post_meta($post_ID, &#39;views&#39;, 1, true);
		  }
	  }
	}
}
add_action(&#39;wp_head&#39;, &#39;record_visitors&#39;);  
function post_views($before = &#39;&#39;, $after = &#39;&#39;, $echo = 1)
{
  global $post;
  $post_ID = $post-&gt;ID;
  $views = (int)get_post_meta($post_ID, &#39;views&#39;, true);
  if ($echo) echo $before, number_format($views), $after;
  else return $views;
}

</pre>
<p>
	原来post_views函数定义在这里，而上面的函数，虽然<a href="http://www.coderli.com">OneCoder</a>对于php完全业余，看不懂，但是也能猜出是计数代码，这肯定和插件里的统计重复了，所以果断去掉。双倍统计消失了，终于正常了：）</p>
<br />
<p>
	<a href="http://www.coderli.com">OneCoder</a>相说的是，可能问题本身具有一定的特殊性，但是解决问题的思路相信总有值得借鉴的地方。不要怕不懂，大胆去猜去做，慢慢的积累就好：）</p>

