---
layout: post
title: OneCoder愚见 Java初学者如何自学和自己定位解决问题
date: 2012-08-21 23:03
author: onecoder
comments: true
categories: [Java, Java基础, 搜索, 自学]
---
<p>
	今天群里(Java Coder群：91513074)的朋友，问我该如何看帮助文档，或者说在遇到问题的时候如何解决。希望我能介绍一下我的方法。</p>
<div>
	这个<a href="http://www.coderli.com">OneCoder</a>其实没有资格高谈阔论，只能说说个人的习惯和方式。自学和自我解决问题确实是一项非常非常重要的能力，远比你现在所会的知识重要的多的多，因为，你未知的永远的无穷的。</div>
<div>
	<ul>
		<li>
			关于API文档</li>
	</ul>
	<div>
		经常有朋友求各种API文档，初学者里最常见的就是要JDK的API文档。这个<a href="http://www.coderli.com">OneCoder</a>个人的习惯是，从来不会去下API文档，那个查找起来也不方便而且也没有代码辅助，而是直接去JDK的源码中看注释，甚至是源码。既方便，又直接，又精确，还接近本质。你下的JDK里，其实都带有源码的，而且默认还是绑定好的。这么方便，为何不看？</div>
</div>
<div>
	比如，你用到了String类中的subString方法，想知道如何使用。你可以去看所谓的API文档，你也可以直接在你的IDE中点开String类，用ctrl+o搜索到subString方法。你可以看到他的注释：</div>
<div>
	<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
/**
     * Returns a new string that is a substring of this string. The
     * substring begins at the specified &lt;code&gt;beginIndex&lt;/code&gt; and
     * extends to the character at index &lt;code&gt;endIndex - 1&lt;/code&gt;.
     * Thus the length of the substring is &lt;code&gt;endIndex-beginIndex&lt;/code&gt;.
     * &lt;p&gt;
     * Examples:
     * &lt;blockquote&gt;&lt;pre&gt;
     * &quot;hamburger&quot;.substring(4, 8) returns &quot;urge&quot;
     * &quot;smiles&quot;.substring(1, 5) returns &quot;mile&quot;
     * &lt;/pre&gt;&lt;/blockquote&gt;
     *
     * @param      beginIndex   the beginning index, inclusive.
     * @param      endIndex     the ending index, exclusive.
     * @return     the specified substring.
     * @exception  IndexOutOfBoundsException  if the
     *             &lt;code&gt;beginIndex&lt;/code&gt; is negative, or
     *             &lt;code&gt;endIndex&lt;/code&gt; is larger than the length of
     *             this &lt;code&gt;String&lt;/code&gt; object, or
     *             &lt;code&gt;beginIndex&lt;/code&gt; is larger than
     *             &lt;code&gt;endIndex&lt;/code&gt;.
     */
</pre>
</div>
<p>
	如果你了解，你就会知道，所有的接口的API都是根据这个Javadoc生成的。顺便，你还可以看到它的实现，加深你的理解，何乐而不为呢？</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
public String substring(int beginIndex, int endIndex) {
        if (beginIndex &lt; 0) {
            throw new StringIndexOutOfBoundsException(beginIndex);
        }
        if (endIndex &gt; count) {
            throw new StringIndexOutOfBoundsException(endIndex);
        }
        if (beginIndex &gt; endIndex) {
            throw new StringIndexOutOfBoundsException(endIndex - beginIndex);
        }
        return ((beginIndex == 0) &amp;&amp; (endIndex == count)) ? this :
            new String(offset + beginIndex, endIndex - beginIndex, value);
    }</pre>
<div>
	<ul>
		<li>
			关于搜索引擎的使用</li>
	</ul>
</div>
<div>
	解决问题最好的办法，当然是去网上搜索。这里<a href="http://www.coderli.com">OneCoder</a>必须要说的是，对于开发人员来说，<a href="http://www.baidu.com/">百度</a>确实不是一个好的搜索引擎，搜索出来的东西相关性和有效性都十分有限。这里，<a href="http://www.coderli.com">OneCoder</a>必须大力的推荐Google。真的不在一个层次上。你可能会说，Google总是被墙。这个，<a href="http://www.coderli.com">OneCoder</a>采用了不太通用的做法，那就是常年购买了一个ssh的代理，专门用于访问google。呵呵：）其实，解决google问题的方法网上还有很多，你也可以去搜索一下。我们可以简单对比一下两个搜索引擎的搜索效果。</div>
<div>
	&nbsp;</div>
<div>
	以&ldquo;Netty 教程&rdquo;这个搜索词为例：</div>
<div style="text-align: center; ">
	<img alt="" src="http://onecoder.qiniudn.com/8wuliao/CcW1CkNN/hcrs8.jpg" style="height: 507px; width: 600px; " /></div>
<div style="text-align: center; ">
	&nbsp;</div>
<div style="text-align: center; ">
	<img alt="" src="http://onecoder.qiniudn.com/8wuliao/CcW1BPpA/pqpG6.jpg" style="height: 422px; width: 600px; " /></div>
<div style="text-align: center; ">
	&nbsp;</div>
<div>
	<div>
		同样的<a href="http://www.coderli.com">OneCoder</a>原创的文章，百度排名靠前的确实被采集站恶意采集的转载文章，而非原创文章。这对于你获取原始的准确的信息是有很大影响的。</div>
	<div>
		&nbsp;</div>
	<div>
		至于相关性、信息的前沿性等方面的对比，<a href="http://www.coderli.com">OneCoder</a>由于一时找不到合适的例子，暂时不做图例。但是，随着你慢慢的使用，你会逐渐发现二者的区别。比如，在你搜索一些前沿性的课题的时候，或者一些罕见的错误的时候。Google往往能给出你有价值的参考信息。</div>
	<div>
		<ul>
			<li>
				关于自我解决问题</li>
		</ul>
	</div>
	<div>
		这个其实说起来比较抽象。你可能会说，这个是经常积累等等。是，这的确需要经验，但是那一点一滴经验又如何去累积呢？难道靠问别人？</div>
	<div>
		&nbsp;</div>
	<div>
		既然提到了搜索引擎，我们就要会用它。好的关键字，能让我更快的接近答案。这里，<a href="http://www.coderli.com">OneCoder</a>的愚见是，不要使用一些例如：怎么办？为什么？等无用的字眼，而是输入关键字。尤其是出现异常的时候，你大可以直接将异常的信息直接粘到搜索引擎中去搜索。（注意：去掉跟你自己代码有关的信息，只要通用的部分。）</div>
	<div>
		比如：今天<a href="http://www.coderli.com">OneCoder</a>遇到一个Hibernate的异常导致的空指针问题。一时没有头绪，遂将所有跟异常信息和部分堆栈粘贴入google，结果就在stackoverflow上，找到非常有价值的信息，从而解决了问题。</div>
	<div>
		<ul>
			<li>
				关于StackOverFlow</li>
		</ul>
		<div>
			一个国外非常著名的问答社区，问题包罗万象，回答都非常的认真友好。<a href="http://www.coderli.com">OneCoder</a>已经在上面解决了无法问题。当然，不是通过直接提问，而还是Google给我搜到的答案。这里还得感谢Google。</div>
	</div>
	<div>
		<ul>
			<li>
				关于其他</li>
		</ul>
		<div>
			其实<a href="http://www.coderli.com">OneCoder</a>用到的工具，就这么简单。源码+Google。除此以外，一个善于总结的善于思考的大脑是更加必不可少的。这就是所谓经验和知识的积累吧。</div>
	</div>
	<div>
		&nbsp;</div>
	<div>
		解决一个问题不是关键，关键是会解决一类问题，更关键的是会独立解决没接触过的问题，这是一种非常重要的能力。我宁愿找一个0基础，但是可以自己动手学会、解决的人，也不需要一个虽然有基础，但是也仅限于此的人。个人遇见，仅供参考。</div>
</div>
<p>
	&nbsp;</p>

