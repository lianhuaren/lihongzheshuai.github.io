---
layout: post
title: 你应该更新的Java知识之构建工具
date: 2013-01-18 09:38
author: onecoder
comments: true
categories: [Gradle, Java, Java基础, Maven]
---
<p>
	今天早上在Mac上鼓捣Java的开发环境，正准备考虑全部采用Gradle代替Maven构建我的开发环境，顺便也学习一下这个时下炒的火热的构建工具。接着就在我的Google Reader上看到了下面这篇文章：<a href="http://dreamhead.blogbus.com/logs/227427912.html">http://dreamhead.blogbus.com/logs/227427912.html</a>，与大家分享一下。</p>
<p>
	时至今日，如果你的Java项目依然需要启动一个特定的工具，比如IDE，才能编译部署，那你的项目不在本文的讨论之中，因为实在落后得太让人无语了。</p>
<p>
	好，回归正题。旧时代的Java世界，构建工具等同于两个词Ant和Maven。</p>
<p>
	Ant源自Make，同JUnit一样，它也是一个航班产物（参见《Ant权威指南》序）。不过，Make的基础注定了它的起点不高，所有一切都要手工打造。我还依稀记得曾几何时，每每开始一个项目都要重新复制一份Ant脚本，修修改改。Maven给了人们新的希望，目录结构的约定、依赖管理、IDE集成，那时看来，几近完美。是的，几近，还差了那么一点点。就是那么一点点，却是致命伤。</p>
<p>
	只要一个项目进行一段时间，一个必然遇到的问题就是，编写新的自动化脚本。因为每个项目都有自己的特定需求，标准做法必然是无法满足的。扩展Maven对任何新手都是一件头疼的事，我们要学会编写插件，要搞清楚生命周期，这时，突然会唤起一丝丝对于ANT的怀念，虽然它做简单事不容易，但做复杂事却也没这么困难。</p>
<p>
	如果那些日子，我们不得不忍受Ant和Maven的不完美，那现在，是时候抛弃它们了。新时代Java构建有两个很好的候选：Gradle和Buildr。在我看来，它们真正比Maven更强大的地方，编写自己的任务更加容易。更值得兴奋的一点是，我们终于可以抛弃冗长的XML，选择一种更优雅的程序设计语言来写代码了，这几乎意味着你可以做到想做的一切。</p>
<p>
	Buildr是Apache出品的构建工具，它以Ruby作为构建脚本。我曾在InfoQ发表过一篇很长的文章《软件开发地基》，讨论软件项目应该具备一些基础构建项，就是以Buildr为基础的。有兴趣的话，可以参考一下。这里就不再赘述。顺便说一下，那篇文章里的内容，除了某些写法现在需要做一些微调，大部分内容依然是适用于大多数Java项目。</p>
<p>
	Gradle现在是整个Java社区的构建新宠，它采用Groovy作为自己的构建语言。如果你知道，Groovy是一门诞生自JVM平台的语言，这就决定了它要比其它移植到JVM上的语言能更好的适应JVM平台，它可以采用更符合Java世界的方式无缝地整合Java既有的程序库，而不必像移植语言那样削足适履。</p>
<p>
	初涉Gradle，最让人吃惊的一点莫过于它详尽的文档，涵盖了Gradle使用的方方面面，这是许多开源软件项目无法媲美，即便早在它的1.0版本尚未发布之时。当然，能做到这一点是因为它背后有一个公司在支撑：GradleWare，这意味着如果你需要商业支持，也是可以的。</p>
<p>
	Gradle 1.0尚未发布之，它就捧回2010年的Spring大奖和入围了2011年的JAX大奖。如果你还需要更多的信心，作为Java开发人员，你不可能不知道Spring，那Spring转投Gradle怀抱，应该是对Gradle最有利的支持了。</p>
<p>
	说了这么多，程序员最喜欢看到的东西还是代码。首先，请自行下载安装Gradle，然后，按照常见的Java项目布局把代码准备好（感谢Maven为我们给予我们约定），比如：</p>
<blockquote>
	<p>
		src/main/java，源代码文件目录<br />
		src/main/resources，资源文件目录<br />
		src/test/java，测试代码目录</p>
</blockquote>
<p>
	下面是一个简单的Gradle构建脚本，将其存放于build.gradle<span style="font-size: 12px;">apply plugin: &#39;java&#39;</span></p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">

repositories {
    mavenCentral()
}
dependencies {
  compile(
    &#39;com.google.guava:guava:13.0.1&#39;,
    &#39;joda-time:joda-time:2.1&#39;
  )

  testCompile(
    &#39;junit:junit:4.10&#39;,
    &#39;org.mockito:mockito-all:1.9.0&#39;
  )
}
（build.gradle）
</pre>
<p>
	接下来的事情就很简单了，在命令行里键入</p>
<blockquote>
	<p>
		&nbsp; gradle build</p>
</blockquote>
<p>
	如果一切正常，我们应该会看到构建成功的字样，然后，到build/lib，你就看到自己构建出来的JAR文件了。当然，这只是一个简单得不能再简单的例子，如果需要了解更多，<a href="http://www.gradle.org/documentation">Gradle那详尽的文档</a>便是最好的去处。</p>
<p style="text-align: justify;">
	关于构建工具的选择考量，我曾经写过一篇blog专门讨论，<a href="http://dreamhead.blogbus.com/logs/117917683.html">《选择，构建工具》</a>。如果你在做构建工具的技术选型，可以参考。</p>

