---
layout: post
title: 《Gradle user guide》翻译 — 7.2 一个基本的Java项目
date: 2013-09-23 10:42
author: onecoder
comments: true
categories: [Gradle, gradle, Java, 用户手册]
---
<div>
	7.2 一个基本的Java项目
	<div>
		&nbsp;</div>
	<div>
		我们来看一个简单的例子。把下面的代码加入你的构建文件，以使用Java插件：</div>
	<div>
		&nbsp;</div>
	<div>
		例子 7.1 使用Java插件</div>
	<div>
		build.gradle</div>
	<div>
		apply plugin: &#39;java&#39;</div>
	<blockquote>
		<div>
			注意：该样例的代码可在Gradle的安装包中的samples/java/quickstart路径下找到。</div>
	</blockquote>
	<div>
		这就是你定义一个Java项目所需要做的一切。这就会在你项目里使用Java插件，该插件会给你的项目增加很多任务。</div>
	<div>
		&nbsp;</div>
	<div>
		Gradle期望在src/main/java路径下找到你项目的源代码，并且测试在src/test/java路径下的代码。同时，在src/main/resources路径下的文件也会作为资源文件包含在JAR包中，并且src/test/resources下的所有文件会包含在classpath下以运行测试程序。所有的输出文件都生成在build目录下，JAR包生成在build/libs目录下。</div>
	<div>
		&nbsp;</div>
	<div>
		7.2.1 编译项目</div>
	<div>
		&nbsp;</div>
	<div>
		Java插件给你的项目构建增加很多任务。然而，只有屈指可数的任务你将会在构建项目的时候用到。最常用的任务是build任务，该任务会对项目执行完整的编译。当你运行gradle build的时候，Gradle会编译并测试你的代码，然后生成一个包含类和资源文件的JAR包。</div>
	<div>
		&nbsp;</div>
	<div>
		例7.2 构建Java项目</div>
	<div>
		&nbsp;</div>
	<br />
	<pre>
gradle build输出
&gt; gradle build
:compileJava
:processResources
:classes
:jar
:assemble
:compileTestJava
:processTestResources
:testClasses
:test
:check
:build
BUILD SUCCESSFUL
Total time: 1 secs

</pre>
	<div>
		&nbsp;</div>
	<div>
		其他一些有用的任务是：</div>
	<div>
		clean</div>
	<div>
		&nbsp; &nbsp; &nbsp;清空build编译目录，删除所有构建文件。</div>
	<div>
		assemble</div>
	<div>
		&nbsp; &nbsp; &nbsp;编译并给你的代码打包，但是不执行单元测试。其他的插件为本任务增加更多的特性支持。例如，如果你使用War插件，那么该任务也会给你的项目编译出War包。</div>
	<div>
		check</div>
	<div>
		&nbsp; &nbsp; &nbsp;编译并测试代码。其他的插件会对该任务提供更多检测支持。例如，如果你使用Code-quality插件，那么该任务会同时对你的代码执行Checkstyle检测。</div>
	<div>
		&nbsp;</div>
	<div>
		7.2.2 外部依赖</div>
	<div>
		&nbsp;</div>
	<div>
		通常，一个Java项目会依赖一些外部的JAR文件。为了在项目中引用这些JAR文件，你需要告诉Gradle哪里可以找到它们。在Gradle里，JAR包等资源放在资源库（repository）中。资源库可以用来查找项目的依赖，也可以用于发布项目归档。在这个样例中，我们将使用公共的Maven库：</div>
	<div>
		&nbsp;</div>
	<div>
		样例7.3 添加Maven库</div>
	<div>
		<pre>
repositories {
    mavenCentral()
}
</pre>
	</div>
	<div>
		我们来添加一些依赖。这里，我们会声明我们产品的类有在编译期依赖于commons collections，同时我们的测试类在编译期依赖于junit:</div>
	<div>
		&nbsp;</div>
	<div>
		样例7.4 添加依赖</div>
	<pre>
dependencies {
       compile group: &#39;commons-collections&#39;, name: &#39;commons-collections&#39;, version: &#39;3.2&#39;
       testCompile group: &#39;junit&#39;, name: &#39;junit&#39;, version: &#39;4.+&#39;
}
</pre>
	<div>
		更多的内容，可在第八章的依赖管理基础中找到。</div>
	<div>
		&nbsp;</div>
	<div>
		7.2.3 自定义项目</div>
	<div>
		&nbsp;</div>
	<div>
		Java插件给你的项目添加了许多属性。这些属性都有默认值，并且通常情况下这些默认值足以满足使用。如果这些值不满足要求，那么修改起来也是很容易的。我们来看一下例子。这里，我们将指定Java项目的版本号，同时指定我们编写代码所使用的Java版本。我们也会在JAR包的描述清单中(manifest)增加一些属性。</div>
	<div>
		&nbsp;</div>
	<div>
		样例7.5 自定义MANIFEST.MF</div>
	<div>
		<br />
		<pre>
sourceCompatibility = 1.5
   version = &#39;1.0&#39;
   jar {
       manifest {
           attributes &#39;Implementation-Title&#39;: &#39;Gradle Quickstart&#39;, &#39;Implementation-Version&#39;: versi
} }

</pre>
	</div>
	<div>
		<table border="1" cellpadding="2" cellspacing="0">
			<tbody>
				<tr>
					<td>
						什么属性是可用的？<br />
						<div>
							你可以使用gradle properties命令去列出项目的属性。这可以让你看到Java插件添加的属性及其默认值。</div>
					</td>
				</tr>
			</tbody>
		</table>
	</div>
	<div>
		Java插件添加的也是规则的任务，如果把它们声明在构建文件里也是完全一样的。这就意味这，你可以使用任何之前章节中介绍的机制去自定义这些任务。例如，你可以设置任务的属性，给任务增加行为，修改任务的依赖，或是完全的替代任务。在我们的样例中，我们将要配置test任务，该任务的类型是Test，当测试执行的时候增加一个系统属性：</div>
	<div>
		&nbsp;</div>
	<div>
		样例7.6 增加一个测试系统属性</div>
	<br />
	<pre>
test {
    systemProperties &#39;property&#39;: &#39;value&#39;
}
</pre>
	<div>
		&nbsp;</div>
	<div>
		7.2.4 发布JAR包</div>
	<div>
		&nbsp;</div>
	<div>
		通常，JAR包需要被发布到某个地方。为了完成这个功能，你需要告诉Gradle把JAR包发布到哪里。在Gradle中，如JAR之类的压缩包都被发布到库中。在我们的样例中，我们将会发布到本地仓库。你也可以发布到一个或多个远端地址。</div>
	<div>
		&nbsp;</div>
	<div>
		样例7.7 发布JAR包</div>
	<div>
		<pre>
uploadArchives {
    repositories {
       flatDir {
           dirs &#39;repos&#39;
} }
}
</pre>
	</div>
	<div>
		执行gradle uploadArchives命令以发布JAR包。</div>
	<div>
		&nbsp;</div>
	<div>
		7.2.5 创建Eclipse工程</div>
	<div>
		&nbsp;</div>
	<div>
		为了把你的项目导入到Eclipse，你需要在构建文件中增加其他插件：</div>
	<div>
		样例 7.8 Eclipse 插件</div>
	<div>
		<pre>
apply plugin: &#39;eclipse&#39;
</pre>
	</div>
	<div>
		现在执行gradle eclipse命令以生成Eclipse工程文件。更多关于Eclipse任务可以在第38章 Eclipse插件中看到。</div>
	<div>
		&nbsp;</div>
	<div>
		7.2.6 概要</div>
	<div>
		&nbsp;</div>
	<div>
		下面是完整的构建文件的样例：</div>
	<div>
		样例7.9 Java样例-完整构建文件</div>
	<br />
	<pre>
   apply plugin: &#39;java&#39;
   apply plugin: &#39;eclipse&#39;
   sourceCompatibility = 1.6
   version = &#39;1.0&#39;
   jar {
       manifest {
           attributes &#39;Implementation-Title&#39;: &#39;Gradle Quickstart&#39;, &#39;Implementation-Version&#39;﻿
   } }
  repositories {
       mavenCentral()
  }
   dependencies {
       compile group: &#39;commons-collections&#39;, name: &#39;commons-collections&#39;, version: &#39;3.2&#39;
       testCompile group: &#39;junit&#39;, name: &#39;junit&#39;, version: &#39;4.+&#39;
  }
   test {
       systemProperties &#39;property&#39;: &#39;value&#39;
   }
   uploadArchives {
       repositories {
          flatDir {
              dirs &#39;repos&#39;
          } 
      }
  }

</pre>
</div>
<p>
	&nbsp;</p>

