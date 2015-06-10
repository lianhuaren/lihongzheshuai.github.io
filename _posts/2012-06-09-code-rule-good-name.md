---
layout: post
title: 代码质量随想录 名字好，误会少
date: 2012-06-09 09:01
author: onecoder
comments: true
categories: [代码质量, 博文转载, 程序员, 规范]
---
<p 0px="" arial="" style="margin: 0px; padding: 0px 0px 15px; color: rgb(85, 85, 85); line-height: 28px; font-family: 宋体, &quot;Arial Narrow&quot;, arial, serif; font-size: 14px;">
	　　要想让标识符的名称更易理解，就应该多考虑考虑此名称是否会被误读。</p>
<span style="color: rgb(85, 85, 85); line-height: 28px; font-family: 宋体, &quot;Arial Narrow&quot;, arial, serif; font-size: 14px;">　　先看两个很容易误读的例子。</span>&nbsp;
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
Object[] results = Database.getAllObjects().filter(&quot;year &lt;= 2011&quot;); 
</pre>
<div>
	&nbsp;　　<span style="color: rgb(85, 85, 85); line-height: 28px; font-family: 宋体, &quot;Arial Narrow&quot;, arial, serif; font-size: 14px;">到底是要选出year小于等于2011的那部分对象，还是选出year大于2011的那部分呢？filter到底是排除（exclude），还是遴选（select）呢？我自己在日常编码中也爱用filter，多半由于习惯。现在自己思量，是得改正了。 再看</span></div>
<div>
	<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
public String clip(String text,int length); //裁掉文本的末尾&nbsp;</pre>
</div>
<p style="margin: 0px; padding: 0px 0px 15px; color: rgb(85, 85, 85); line-height: 28px; font-family: 宋体, &quot;Arial Narrow&quot;, arial, serif; font-size: 14px;">
	clip方法有歧义：到底是去掉文本后的length个字符，还是从头开始截取最大length个字符呢？比如clip(&quot;Java&quot;,2);到底是&quot;va&quot;还是&quot;Ja&quot;？如果是前者应该叫removeLast，如果是后者则应叫truncate。而且length也有毛病，到底以什么为单位？字节？字符？还是词语？如果是字符，应该是truncate(String text,int maxCharCount)。</p>
<p style="margin: 0px; padding: 0px 0px 15px; color: rgb(85, 85, 85); line-height: 28px; font-family: 宋体, &quot;Arial Narrow&quot;, arial, serif; font-size: 14px;">
	　　归纳起来说，以下几种情形应格外注重选取避免误解的名称。</p>
<p style="margin: 0px; padding: 0px 0px 15px; color: rgb(85, 85, 85); line-height: 28px; font-family: 宋体, &quot;Arial Narrow&quot;, arial, serif; font-size: 14px;">
	<strong style="margin: 0px; padding: 0px;">1.以常量表示包含端点的上限或下限时，应分别用MAX与MIN做前缀。</strong></p>
<p style="margin: 0px; padding: 0px 0px 15px; color: rgb(85, 85, 85); line-height: 28px; font-family: 宋体, &quot;Arial Narrow&quot;, arial, serif; font-size: 14px;">
	　　例如CART_TOO_BIT_LIMIT=10到底是说购物车中最多放10件商品还是11件？抑或是9件？改为MAX_ITEMS_IN_CART = 10则很清楚：最多10件。</p>
<strong style="margin: 0px; padding: 0px; color: rgb(85, 85, 85); line-height: 28px; font-family: 宋体, &quot;Arial Narrow&quot;, arial, serif; font-size: 14px;">2.在表达包含左右端点的区间时，应用first及last。</strong><br />
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
public void printIntegerInRange(int start,int stop){...} 
... &nbsp;
printIntegerInRange(2,6);&nbsp;</pre>
<p style="margin: 0px; padding: 0px 0px 15px; color: rgb(85, 85, 85); line-height: 28px; font-family: 宋体, &quot;Arial Narrow&quot;, arial, serif; font-size: 14px;">
	到底打印[2,3,4,5]还是[2,3,4,5,6]？如果是后者，应该是printIntegerInRange(int first,int last)。</p>
<p style="margin: 0px; padding: 0px 0px 15px; color: rgb(85, 85, 85); line-height: 28px; font-family: 宋体, &quot;Arial Narrow&quot;, arial, serif; font-size: 14px;">
	<strong style="margin: 0px; padding: 0px;">3.在表达包含左端点而不含右端点的区间时，应当使用begin与end。</strong></p>
<p style="margin: 0px; padding: 0px 0px 15px; color: rgb(85, 85, 85); line-height: 28px; font-family: 宋体, &quot;Arial Narrow&quot;, arial, serif; font-size: 14px;">
	　　英文中没有哪个常用词的字面意思能表示&ldquo;区段内最后一个值的紧下一个值&rdquo;这个意思，所以使用end只是约定成俗而已，并不精确。例如public void printEventsInRange(String begin,String end)，可以使用如下参数来调用：printEventsInRange(&quot;OCT 16 00:00&quot;, &quot;OCT 17 00:00&quot;)，这样的话，一般人都能理解右端点（&quot;OCT 17 00:00&quot;）不含在范围内。如果用public void printEventsInRange(String first,String last)，则是printEventsInRange(&quot;OCT 16 00:00&quot;, &quot;OCT 16 23:59&quot;)。</p>
<strong style="margin: 0px; padding: 0px; color: rgb(85, 85, 85); line-height: 28px; font-family: 宋体, &quot;Arial Narrow&quot;, arial, serif; font-size: 14px;">4.使用判断词来消除boolean变量的歧义。</strong><br />
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
bool readPassword = true;&nbsp;</pre>
<p style="margin: 0px; padding: 0px 0px 15px; color: rgb(85, 85, 85); line-height: 28px; font-family: 宋体, &quot;Arial Narrow&quot;, arial, serif; font-size: 14px;">
	到底是当前需要读取密码，还是密码已经被读取过了？前者应是needPassword，后者应是userIsAuthenticated。</p>
<p style="margin: 0px; padding: 0px 0px 15px; color: rgb(85, 85, 85); line-height: 28px; font-family: 宋体, &quot;Arial Narrow&quot;, arial, serif; font-size: 14px;">
	　　使用is、has、can、should等词汇来让boolean变量与方法的意图更加清晰，尤其是在那些不需要申明方法或函数返回类型的编程语言中。例如：spaceLeft()到底是返回剩下的空间大小，还是返回是否有剩余空间？根据是简单获取还是复杂计算，前者应命名为getLeftSpaceInPixel()或calcLeftSpacePx()，分别指示轻量级（get）和重量级（calculate或compute）的两种获取办法；而后者则应是hasSpaceLeft()，只说有没有剩余空间，不谈具体的量。</p>
<p style="margin: 0px; padding: 0px 0px 15px; color: rgb(85, 85, 85); line-height: 28px; font-family: 宋体, &quot;Arial Narrow&quot;, arial, serif; font-size: 14px;">
	<strong style="margin: 0px; padding: 0px;">5.避免在boolean命名中使用否定形式。</strong></p>
<span style="color: rgb(85, 85, 85); line-height: 28px; font-family: 宋体, &quot;Arial Narrow&quot;, arial, serif; font-size: 14px;">　　例如：</span><br />
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
bool disableSSL = false; 
</pre>
<br />
不如下面这种命名方式清晰：<br />
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
bool useSSL = true;&nbsp;</pre>
<p style="margin: 0px; padding: 0px 0px 15px; color: rgb(85, 85, 85); line-height: 28px; font-family: 宋体, &quot;Arial Narrow&quot;, arial, serif; font-size: 14px;">
	<strong style="margin: 0px; padding: 0px;">6.不要同约定成俗的命名方式相违逆。</strong></p>
<p style="margin: 0px; padding: 0px 0px 15px; color: rgb(85, 85, 85); line-height: 28px; font-family: 宋体, &quot;Arial Narrow&quot;, arial, serif; font-size: 14px;">
	　　例如getXXX()格式的方法一般有两个隐含意义：1.该操作为轻量级。2.该操作返回所在类的某个成员。</p>
<span style="color: rgb(85, 85, 85); line-height: 28px; font-family: 宋体, &quot;Arial Narrow&quot;, arial, serif; font-size: 14px;">　　如下统计算数平均数的方法名称即为不宜：</span><br />
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
public class SampleCollector { 
  public void add(double sample) { ... } 
 
  public double getMean() { 
    ... // 叠加所有采样值并返回&ldquo;总和/样本数&rdquo; 
  } 
... 
} 
</pre>
getMean()并非轻量级操作，且不返回本类某个成员。不如叫它computeMean()更好，compute会引人联想该操作是不是稍为复杂一些，耗时一些。如果非要用getMean做名称的话，那么mean应被纳入缓存机制。例如：<br />
<node>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
private boolean meanCached;//计算完样本后置为true，样本改动时置为false 
... 
 
public double getMean() { 
  if(!meanCached){ 
    ... // 叠加所有采样值 
    mean=sampleSum/sampleCount; 
    meanCached=true; 
  } 
  return mean; 
} 
</pre>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
void ShrinkList(list&lt;Node&gt;&amp; list, int max_size) { 
  while (list.size() &gt; max_size) { 
    FreeNode(list.back()); 
    list.pop_back(); 
  } 
} 
</pre>
<br />
<p style="margin: 0px; padding: 0px 0px 15px; color: rgb(85, 85, 85); line-height: 28px; font-family: 宋体, &quot;Arial Narrow&quot;, arial, serif; font-size: 14px; ">
	size()操作的时间复杂度为O(1)应是大多数人的共识，可是恰恰list的size()是时间复杂度为O(n)的操作，这导致整个函数的复杂度变为O(n<sup style="margin: 0px; padding: 0px;">2</sup>)。按理说size()应该叫为countSize() 或countElements()，以体现其重量级运算的特质来，不过，为了和其余容器类相符合，还是叫成size了。所幸新版C++规范强制要求size操作的时间复杂度为O(1)了（ARC书的作者这么说的，我未查证。大家帮忙在C++11规范中查证此事。原有规范只是&ldquo;建议&rdquo;它应具有常数时间复杂度，并未强制）。</p>
<p style="margin: 0px; padding: 0px 0px 15px; color: rgb(85, 85, 85); line-height: 28px; font-family: 宋体, &quot;Arial Narrow&quot;, arial, serif; font-size: 14px;">
	　　小翔以为，如果某个抽象接口定义了一个貌似轻量级的简单操作，如Collection的size()，则子类对象在实现时应该尽量降低时间复杂度。实在不能时甚至可以考虑抛出异常或对客户提出警告。根本的解决办法还是学习C++规范那样，给出一个建议的时间复杂度来。</p>
<p style="margin: 0px; padding: 0px 0px 15px; color: rgb(85, 85, 85); line-height: 28px; font-family: 宋体, &quot;Arial Narrow&quot;, arial, serif; font-size: 14px;">
	<strong style="margin: 0px; padding: 0px;">7.在多个候选名称中取舍时应该仔细质询其可能带来的歧义。</strong></p>
<span style="color: rgb(85, 85, 85); line-height: 28px; font-family: 宋体, &quot;Arial Narrow&quot;, arial, serif; font-size: 14px;">　　例如有两份相似的服务器配置参数文件：</span><br />
<pre class="brush:xml;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
config_id: 100 
description: &quot;increase font size to 14pt&quot; 
traffic_fraction: 5% 
... 
</pre>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
config_id: 101 
description: &quot;increase font size to 13pt&quot; 
[其余参数与前一份相同] 
</pre>
我们现在想通过某个机制复用整套参数，例如这样：<br />
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
config_id: 101 
想要复用的配置文件id: 100 
[其余参数与前一份相同] &nbsp;</pre>
<p style="margin: 0px; padding: 0px 0px 15px; color: rgb(85, 85, 85); line-height: 28px; font-family: 宋体, &quot;Arial Narrow&quot;, arial, serif; font-size: 14px;">
	那么，这个&ldquo;想要复用的配置文件id&rdquo;，应该怎么起名呢？备选关键词有：template、reuse、copy和inherit。</p>
<p style="margin: 0px; padding: 0px 0px 15px; color: rgb(85, 85, 85); line-height: 28px; font-family: 宋体, &quot;Arial Narrow&quot;, arial, serif; font-size: 14px;">
	　　template很模糊：&ldquo;template: 100&rdquo;到底是说自己是一份名叫&ldquo;100&rdquo;模板，还是说使用一个名叫&ldquo;100&rdquo;的模板作为其基础参数？况且模板这个概念太过抽象，给人感觉需要以具体内容填充它。</p>
<p style="margin: 0px; padding: 0px 0px 15px; color: rgb(85, 85, 85); line-height: 28px; font-family: 宋体, &quot;Arial Narrow&quot;, arial, serif; font-size: 14px;">
	　　&ldquo;reuse: 100&rdquo;到底是说这份参数最多可以使用100次，还是说复用名为&ldquo;100&rdquo;的那份配置文件中的参数？</p>
<p style="margin: 0px; padding: 0px 0px 15px; color: rgb(85, 85, 85); line-height: 28px; font-family: 宋体, &quot;Arial Narrow&quot;, arial, serif; font-size: 14px;">
	　　&ldquo;copy: 100&rdquo;是第100份拷贝吗？还是说拷贝自编号为&ldquo;100&rdquo;的那套配置？后者不如叫copy_config_from更好。</p>
<p style="margin: 0px; padding: 0px 0px 15px; color: rgb(85, 85, 85); line-height: 28px; font-family: 宋体, &quot;Arial Narrow&quot;, arial, serif; font-size: 14px;">
	　　&ldquo;inherit: 100&rdquo;，inherit这个词，大多数程序员很熟悉，且与日常生活的&ldquo;财产继承&rdquo;概念可相比拟，所以引起的误解相对较少。可以扩充为inherit_config_from来更精确地阐明这个意思。</p>
<p style="margin: 0px; padding: 0px 0px 15px; color: rgb(85, 85, 85); line-height: 28px; font-family: 宋体, &quot;Arial Narrow&quot;, arial, serif; font-size: 14px;">
	　　综上，copy_config_from或inherit_config_from应为最终中选名称。</p>
<p style="margin: 0px; padding: 0px 0px 15px; color: rgb(85, 85, 85); line-height: 28px; font-family: 宋体, &quot;Arial Narrow&quot;, arial, serif; font-size: 14px;">
	　　总之，好的标识符名称可以尽量消除代码阅读者的误解，提高代码可读性与可维护性，亦能促进业务交流。所以应当仔细考究，尽量选取免于误会的名称，尤其是遇到&ldquo;filter、length和limit&rdquo;这些模棱两可的词语时。此外，区间与上下限含不含端点、boolean类型的标识符会不会引起误解、方法名称所隐含的意义是否符合常识，这些问题也应该在起名时反覆考量。</p>
<p style="margin: 0px; padding: 0px 0px 15px; color: rgb(85, 85, 85); line-height: 28px; font-family: 宋体, &quot;Arial Narrow&quot;, arial, serif; font-size: 14px;">
	　　用了两篇文章才讲完给标识符起名的事情，可见其的确关乎代码质量的提升。下一篇我们谈谈代码的排版问题。</p>
<cite>爱飞翔 2012年6月4日</cite> <cite>本文使用Creative Commons BY-NC-ND 3.0协议（创作共用 自由转载-保持署名-非商业使用-禁止衍生）发布。</cite><br />
<cite>本文网址：<a href="http://agilemobidev.net/eastarlee/code-quality/think_in_code_quality_3_good_name_zh_cn/">http://agilemobidev.com/eastarlee/code-quality/think_in_code_quality_3_good_name_zh_cn/</a></cite> <cite>本文出自 &ldquo;<a href="http://eastarlee.blog.51cto.com/">软件设计部落格</a>&rdquo; 博客，请务必保留此出处<a href="http://eastarlee.blog.51cto.com/3947664/888477">http://eastarlee.blog.51cto.com/3947664/888477</a></cite></node>
