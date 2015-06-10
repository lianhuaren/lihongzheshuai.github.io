---
layout: post
title: 未初始化的String相“+”为什么会打印出“nullnull”
date: 2012-06-14 21:13
author: onecoder
comments: true
categories: [Java, Java基础, null, String]
---
<p>
	今天在我的一个qq群里有人问了这样一个问题。</p>
<pre class="brush:java;title:'Java';first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
private static String a;

private static String b;

public static void main(String[] args) {

    String c = a+b;

    System.out.println(c);

}
</pre>
<p>
	输出是&rdquo;<span style="color: #ff0000;">nullnull</span>&rdquo;。问为什么是这样。</p>
<p>
	其实问题并不复杂，很多同学也觉得自己知道原因，遂不予关注。但是我相信还是有初学Java的同学在这里是存在误会的。很典型的误会就是如一个群里的朋友说的String类型的变量如果没有显示初始化，默认的值就是&rdquo;null&rdquo;。支持的理由就是</p>
<pre class="brush:java;title:'Java';first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
private static String a;

public static void main(String[] args) {

System.out.println(a);

}
</pre>
<p>
	输出是&ldquo;<span style="color: #ff0000;">null</span>&rdquo;。</p>
<p>
	这个现实确实很容易迷惑一些初学的人，包括我也忽略了挺久。其实呢证明这种想法错误很简单。如果默认值是&ldquo;null&rdquo;，那么就意味着，该变量不是空(null)。而是字符串的&rdquo;null&rdquo;。</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
private static String a;

public static void main(String[] args) {

System.out.println(a==null);

System.out.println(&quot;null&quot;.equals(a));

}
</pre>
<p>
	上述代码输出分别是true,false</p>
<p>
	说明String类型变量a，其实是空(null)，而并没有被赋值。那么打印出null是为什么呢？我们查看PrintStream的源码就很清晰的明白了，其实是Java在println的时候进行了处理。</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
public void print(String s) {

if (s == null) {

s = &quot;null&quot;;

}

write(s);

}
</pre>
<p>
	回到开头的问题，既然没有初始化赋值，那么输出为什么是&rdquo;nullnull&rdquo;，两个&rdquo;null&rdquo;连接的结果呢。这里略微细说一下，查看编译过的class文件，我们可以看到</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
public static void main(java.lang.String[] args);

0 new java.lang.StringBuilder [19]

3 dup

4 getstatic cn.home.pratice.jdk.string.StringMain.a : java.lang.String [21]

7 invokestatic java.lang.String.valueOf(java.lang.Object) : java.lang.String [23]

10 invokespecial java.lang.StringBuilder(java.lang.String) [29]

13 getstatic cn.home.pratice.jdk.string.StringMain.b : java.lang.String [32]

16 invokevirtual java.lang.StringBuilder.append(java.lang.String) : java.lang.StringBuilder [34]

19 invokevirtual java.lang.StringBuilder.toString() : java.lang.String [38]

22 astore_1 [c][/c]

23 getstatic java.lang.System.out : java.io.PrintStream [42]

26 aload_1 [c][/c]

27 invokevirtual java.io.PrintStream.println(java.lang.String) : void [48]

30 return System.out.println(c);

}
</pre>
<p>
	String的相加实际在变异后被处理成了StringBuilder的append.(注：我的JDK是1.6.0_u29)。那么好，我们就应该查看StringBuilder的源码，发现是调用的父类里的方法，继续查看，道理就在这里。</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
public StringBuilder append(String str) {

super.append(str);

return this;

}

public AbstractStringBuilder append(String str) {

if (str == null) str = &quot;null&quot;;

int len = str.length();

if (len == 0) return this;

int newCount = count + len;

if (newCount &gt; value.length)

expandCapacity(newCount);

str.getChars(0, len, value, count);

count = newCount;

return this;

}
</pre>
<p>
	原来也是对空null，进行了特殊的处理，那么输出是&rdquo;nullnull&rdquo;，自然也就明白了。</p>
<p>
	这里我想说的是，很多问题，可能表面上很简单，或者我们可能会有很多想当然的想法，不过还是眼见为实，而且所有代码都放在那里，我们为什么不勤快的多翻开看看其中的实现，道理自然就在眼前。多动手，丰衣足食：）</p>

