---
layout: post
title: Java 8的lambda 表达式
date: 2012-06-26 22:43
author: onecoder
comments: true
categories: [java8, Java基础, lambda, 博文转载]
---
<p style="padding: 0px; margin: 0px 0px 10px; font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px;  ">
	Java 8 预计将在 2013 年发布，Java 8 将支持 Lambda 功能，尽管该规范还在不断的变化，但是 Java 8 的开发版已经实现了对 lambda 的支持。</p>
<p style="padding: 0px; margin: 0px 0px 10px; font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px;  ">
	关于 lambda 表达式的定义请看<a href="http://zh.wikipedia.org/wiki/%CE%9B%E6%BC%94%E7%AE%97" rel="nofollow" style="padding: 0px; margin: 0px; color: rgb(68, 102, 187); outline: 0px; " target="_blank">维基百科</a>。</p>
<p style="padding: 0px; margin: 0px 0px 10px; font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px;  ">
	该文章将带你熟悉 lambda 语法，以及使用集合 API 中的 lambda 以及相关的语言增强，本文所有的代码都是在&nbsp;<a href="http://jdk8.java.net/lambda/" rel="nofollow" style="padding: 0px; margin: 0px; color: rgb(68, 102, 187); outline: 0px; ">JDK 8 lambda build b39</a>&nbsp;编译。</p>
<h3 style="padding: 0px; margin: 20px 0px 10px; font-family: 微软雅黑, Verdana, sans-serif, 宋体; line-height: 22px;  ">
	功能接口</h3>
<p style="padding: 0px; margin: 0px 0px 10px; font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px;  ">
	只包含一个方法的接口被称为功能接口，Lambda 表达式用用于任何功能接口适用的地方。</p>
<p style="padding: 0px; margin: 0px 0px 10px; font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px;  ">
	<a href="http://docs.oracle.com/javase/7/docs/api/java/awt/event/ActionListener.html" rel="nofollow" style="padding: 0px; margin: 0px; color: rgb(68, 102, 187); outline: 0px; "><code style="padding: 0px; margin: 0px; ">java.awt.event.ActionListener</code></a>&nbsp;就是一个功能接口，因为它只有一个方法：<code style="padding: 0px; margin: 0px; ">void&nbsp;actionPerformed(ActionEvent)</code>. 在 Java 7 中我们会编写如下代码：</p>
<br />
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
button.addActionListener(new ActionListener() { 
    public void actionPerformed(ActionEvent e) { 
        ui.dazzle(e.getModifiers());
    }
});
</pre>
<span style="font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px;  ">而 Java 8 中可以简化为：</span><br />
<br />
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
button.addActionListener(e -&gt; { ui.dazzle(e.getModifiers()); });
</pre>
<p style="padding: 0px; margin: 0px 0px 10px; font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px;  ">
	编译器知道lambda 表达式必须符合&nbsp;<code style="padding: 0px; margin: 0px; ">void&nbsp;actionPerformed(ActionEvent)</code>&nbsp;方法的定义。看起来 lambda 实体返回 void，实际上它可以推断出参数 e 的类型是&nbsp;<code style="padding: 0px; margin: 0px; ">java.awt.event.ActionEvent</code>.</p>
<h3 style="padding: 0px; margin: 20px 0px 10px; font-family: 微软雅黑, Verdana, sans-serif, 宋体; line-height: 22px;  ">
	函数集合</h3>
<p style="padding: 0px; margin: 0px 0px 10px; font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px;  ">
	Java 8 的类库包含一个新的包&nbsp;<code style="padding: 0px; margin: 0px; ">java.util.functions</code>&nbsp;，这个包中有很多新的功能接口，这些接口可与集合 API 一起使用。</p>
<h4 style="padding: 0px; margin: 0px; font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px;  ">
	java.util.functions.Predicate</h4>
<p style="padding: 0px; margin: 0px 0px 10px; font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px;  ">
	使用谓词 (Predicate) 来筛选集合：</p>
<br />
<br />
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
List&lt;String&gt; names = Arrays.asList(&quot;Alice&quot;, &quot;Bob&quot;, &quot;Charlie&quot;, &quot;Dave&quot;);
List&lt;String&gt; filteredNames = names
        .filter(e -&gt; e.length() &gt;= 4)
        .into(new ArrayList&lt;String&gt;());
for (String name : filteredNames) {
    System.out.println(name);
}
</pre>
<span style="font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px;  ">这里我们有两个新方法：</span>
<ul style="padding: 0px; margin: 10px 0px 20px 20px; list-style-position: inside; line-height: 20px; font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px;  ">
	<li style="padding: 0px; margin: 0px; ">
		<code style="padding: 0px; margin: 0px; ">Iterable&lt;T&gt;&nbsp;<b style="padding: 0px; margin: 0px; ">filter(</b>Predicate&lt;?&nbsp;super&nbsp;T&gt;<b style="padding: 0px; margin: 0px; ">)</b></code>&nbsp;用于获取元素满足某个谓词返回 true 的结果</li>
	<li style="padding: 0px; margin: 0px; ">
		<code style="padding: 0px; margin: 0px; ">&lt;A&nbsp;extends&nbsp;Fillable&lt;?&nbsp;super&nbsp;T&gt;&gt;&nbsp;A&nbsp;<b style="padding: 0px; margin: 0px; ">into(</b>A<b style="padding: 0px; margin: 0px; ">)</b></code>&nbsp;将用返回的结果填充&nbsp;<code style="padding: 0px; margin: 0px; ">ArrayList</code></li>
</ul>
<div style="padding: 0px; margin: 0px; ">
	<h4 style="padding: 0px; margin: 0px; font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px;  ">
		java.util.functions.Block</h4>
	<p style="padding: 0px; margin: 0px 0px 10px; font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px;  ">
		我们可使用一个新的迭代器方法来替换 for 循环&nbsp;<code style="padding: 0px; margin: 0px; ">void&nbsp;<b style="padding: 0px; margin: 0px; ">forEach(</b>Block&lt;?&nbsp;super&nbsp;T&gt;<b style="padding: 0px; margin: 0px; ">)</b></code>:</p>
	<br />
	<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
List&lt;String&gt; names = Arrays.asList(&quot;Alice&quot;, &quot;Bob&quot;, &quot;Charlie&quot;, &quot;Dave&quot;);
names
   .filter(e -&gt; e.length() &gt;= 4)
   .forEach(e -&gt; { System.out.println(e); });
</pre>
</div>
<div style="padding: 0px; margin: 0px; ">
	<code style="padding: 0px; margin: 0px; font-size: 14px; line-height: 22px;  ">forEach()</code><span style="font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px;  ">&nbsp;方法是&nbsp;</span><a href="http://gafter.blogspot.co.uk/2007/07/internal-versus-external-iterators.html" rel="nofollow" style="padding: 0px; margin: 0px; color: rgb(68, 102, 187); outline: 0px; font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px;  ">internal iteration</a><span style="font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px;  ">&nbsp;的一个实例：迭代过程在&nbsp;&nbsp;</span><code style="padding: 0px; margin: 0px; font-size: 14px; line-height: 22px;  ">Iterable</code><span style="font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px;  ">&nbsp;和&nbsp;</span><code style="padding: 0px; margin: 0px; font-size: 14px; line-height: 22px;  ">Block</code><span style="font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px;  ">&nbsp;内部进行，每次可访问一个元素。</span>
	<p style="padding: 0px; margin: 0px 0px 10px; font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px;  ">
		最后的结果就是用更少的代码来处理集合：</p>
	<br />
	<br />
	<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
List&lt;String&gt; names = Arrays.asList(&quot;Alice&quot;, &quot;Bob&quot;, &quot;Charlie&quot;, &quot;Dave&quot;);
names
   .mapped(e -&gt; { return e.length(); })
   .asIterable() // returns an Iterable of BiValue elements
                 // an element&#39;s key is the person&#39;s name, its value is the string length
   .filter(e -&gt; e.getValue() &gt;= 4)
   .sorted((a, b) -&gt; a.getValue() - b.getValue())
   .forEach(e -&gt; { System.out.println(e.getKey() + &#39;\t&#39; + e.getValue()); });
</pre>
</div>
<span style="font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px;  ">这样做的优点是：</span>
<dl style="padding: 0px; margin: 0px; font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px;  ">
	<dt style="padding: 0px; margin: 0px; ">
		元素在需要的时候才进行计算</dt>
	<dd style="padding: 0px; margin: 0px; ">
		如果我们取一个上千个元素的集合的前三条时，其他元素就不会被映射</dd>
	<dt style="padding: 0px; margin: 0px; ">
		鼓励使用方法链</dt>
	<dd style="padding: 0px; margin: 0px; ">
		我们无需才存储中间结果来构建新的集合</dd>
	<dt style="padding: 0px; margin: 0px; ">
		内部迭代过程因此大多数细节</dt>
	<dd style="padding: 0px; margin: 0px; ">
		例如，我们可以通过下面代码来并行 map() 操作&nbsp;<br style="padding: 0px; margin: 0px; " />
		writing&nbsp;<code style="padding: 0px; margin: 0px; ">myCollection.parallel().map(e&nbsp;?&gt;&nbsp;e.length())</code>.</dd>
</dl>
<h3 style="padding: 0px; margin: 20px 0px 10px; font-family: 微软雅黑, Verdana, sans-serif, 宋体; line-height: 22px;  ">
	方法引用</h3>
<p style="padding: 0px; margin: 0px 0px 10px; font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px;  ">
	我们可通过 :: 语法来引用某个方法。方法引用被认为是跟 lambda 表达式一样的，可用于功能接口所适用的地方。</p>
<span style="font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px;  ">我们可以引用一个静态方法：</span><br />
<br />
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
executorService.submit(MethodReference::sayHello);

private static void sayHello() {
        System.out.println(&quot;hello&quot;);
}
</pre>
<br />
<span style="font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px;  ">或者是一个实例的方法：</span><br />
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
Arrays.asList(&quot;Alice&quot;, &quot;Bob&quot;, &quot;Charlie&quot;, &quot;Dave&quot;).forEach(System.out::println);
</pre>
<span style="font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px;  ">我们也可以创建工程方法并将构造器引用赋值给&nbsp;</span><code style="padding: 0px; margin: 0px; font-size: 14px; line-height: 22px;  ">java.util.functions.Factory</code><span style="font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px;  ">:</span><br />
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
Factory&lt;Biscuit&gt; biscuitFactory = Biscuit::new;
Biscuit biscuit = biscuitFactory.make();
</pre>
<span style="font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px;  ">最后，我们创建一个引用到随意实例的例子：</span><br />
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
interface Accessor&lt;BEAN, PROPERTY&gt; {
        PROPERTY access(BEAN bean);
}

public static void main(String[] args) {
        Address address = new Address(&quot;29 Acacia Road&quot;, &quot;Tunbridge Wells&quot;);
        Accessor&lt;Address, String&gt; accessor = Address::getCity;
        System.out.println(accessor.access(address));
}
</pre>
<span style="font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px;  ">这里我们无需绑定方法引用到某个实例，我们直接将实例做为功能接口的参数进行传递。</span><br />
<h3 style="padding: 0px; margin: 20px 0px 10px; font-family: 微软雅黑, Verdana, sans-serif, 宋体; line-height: 22px;  ">
	默认方法</h3>
<p style="padding: 0px; margin: 0px 0px 10px; font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px;  ">
	直到今天的 Java ，都不可能为一个接口添加方法而不会影响到已有的实现类。而 Java 8 允许你为接口自身指定一个默认的实现：</p>
<br />
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
interface Queue {
        Message read();
        void delete(Message message);
        void deleteAll() default {
                Message message;
                while ((message = read()) != null) {
                        delete(message);
                }
        }
}
</pre>
<span style="font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px;  ">子接口可以覆盖默认的方法：</span><br />
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
interface BatchQueue extends Queue {
        void setBatchSize(int batchSize);
        void deleteAll() default {
                setBatchSize(100);
                Queue.super.deleteAll();
        }
}
</pre>
<span style="font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px;  ">或者子接口也可以通过重新声明一个没有方法体的方法来删除默认的方法：</span><br />
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
interface FastQueue extends Queue {
        void deleteAll();
}
</pre>
<span style="font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px;  ">这个将强制所有实现了 FastQueue 的类必须实现 deleteAll() 方法。</span>
<h3 style="padding: 0px; margin: 20px 0px 10px; font-family: 微软雅黑, Verdana, sans-serif, 宋体; line-height: 22px;  ">
	HotSpot 实现</h3>
<p style="padding: 0px; margin: 0px 0px 10px; font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px;  ">
	lambda 不只是可以减少很多代码的编写，其字节码和运行时的实现也比 Java 7 中的匿名类的效率更高。针对每一个 lambda 表达式，编译器都会创建一个对应的形如 lambda$1() 这样的方法。这个过程被称之为&nbsp;<i style="padding: 0px; margin: 0px; ">lambda body desugaring</i>. 当遇见一个 lambda 表达式，编译器将会发起一个<a href="http://blog.headius.com/2008/09/first-taste-of-invokedynamic.html" rel="nofollow" style="padding: 0px; margin: 0px; color: rgb(68, 102, 187); outline: 0px; "><code style="padding: 0px; margin: 0px; ">invokedynamic</code></a>&nbsp;调用，并从目标功能接口中获取返回值。</p>
<h3 style="padding: 0px; margin: 20px 0px 10px; font-family: 微软雅黑, Verdana, sans-serif, 宋体; line-height: 22px;  ">
	深入阅读</h3>
<p style="padding: 0px; margin: 0px 0px 10px; font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px;  ">
	本文很多内容都基于 Brian Goetz 的文章：<a href="http://cr.openjdk.java.net/~briangoetz/lambda/lambda-state-4.html" rel="nofollow" style="padding: 0px; margin: 0px; color: rgb(68, 102, 187); outline: 0px; ">State of the Lambda</a>,&nbsp;<a href="http://cr.openjdk.java.net/~briangoetz/lambda/collections-overview.html" rel="nofollow" style="padding: 0px; margin: 0px; color: rgb(68, 102, 187); outline: 0px; ">State of the Lambda: Libraries Edition</a>&nbsp;and&nbsp;<a href="http://cr.openjdk.java.net/~briangoetz/lambda/lambda-translation.html" rel="nofollow" style="padding: 0px; margin: 0px; color: rgb(68, 102, 187); outline: 0px; ">Translation of Lambda Expressions</a>. 这些文字详细描述了 lambda 语法、变量捕获、类型接口和编译等内容。</p>
<p style="padding: 0px; margin: 0px 0px 10px; font-family: 微软雅黑, Verdana, sans-serif, 宋体; font-size: 14px; line-height: 22px;  ">
	<a href="http://datumedge.blogspot.co.uk/2012/06/java-8-lambdas.html" rel="nofollow" style="padding: 0px; margin: 0px; color: rgb(68, 102, 187); outline: 0px; " target="_blank">英文原文</a>，<a href="http://www.oschina.net/question/12_59047" rel="nofollow" style="padding: 0px; margin: 0px; color: rgb(68, 102, 187); outline: 0px; " target="_blank">OSCHINA</a>原创翻译</p>

