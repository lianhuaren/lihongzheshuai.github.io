---
layout: post
title: 一个容易忽略的Java空指针问题
date: 2012-07-04 11:37
author: onecoder
comments: true
categories: [Java, Java基础, nullpointexception]
---
<span style="font-family: Tahoma; font-size: 14px; ">今天有人提出了代码中一个的NullPointException问题。这类问题，很好解决，找到所在行，一看便知。</span>
<div style="font-family: Tahoma; font-size: 14px; ">
	&nbsp;</div>
<div style="font-family: Tahoma; font-size: 14px; ">
	但是这次，有点意外。抛异常的行，只是一个简单的Pojo的get、set方法。形如：</div>
<blockquote>
	<div style="font-family: Tahoma; font-size: 14px; ">
		a.setSize(b.getSize());</div>
</blockquote>
<div style="font-family: Tahoma; font-size: 14px; ">
	<div>
		很自然的想到b会为null。但是前面的代码已经用过b了，也就是说，如果b是null。早就抛出空指针了。a是新new的。不会是null。set方法内部也只是赋值。this.size = size。</div>
	<div>
		&nbsp;</div>
	<div>
		一时间，我迷惑了。再仔细看代码，发现.b.getSize获取的类型是Long。而a.setSize，传入的类型是long。<font color="#ff0000">惊醒！</font></div>
	<div>
		&nbsp;</div>
	<div>
		这种包装类到基本类型的转换，是会导致空指针的发生的。到此，bug是定位了。但是，你不想知道为什么这种强转会抛空指针吗？首先，应该想到的是，通俗的说，NullPointException的一般情况，就是用null对象调用方法。那么自然联想到，这里究竟是掉了什么方法？</div>
	<div>
		&nbsp;</div>
	<div>
		很简单，看编译过的字节码便知：<br />
		<br />
		<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
  1  astore_1 [l]
     2  aload_1 [l]
     3  invokevirtual java.lang.Long.longValue() : long [16]
     6  lstore_2 [m]
</pre>
	</div>
	<br />
	原来这种强转是调用了Long中的longValue()方法。自然符合用null对象调用方法，抛出空指针异常，也就不奇怪了。</div>

