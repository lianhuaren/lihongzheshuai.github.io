---
layout: post
title: 教你快速写出多线程Junit单元测试用例 - GroboUtils
date: 2012-06-13 13:17
author: onecoder
comments: true
categories: [groboutils, junit, 单元测试, 单元测试, 多线程]
---
<p>
	写过Junit单元测试的同学应该会有感觉，Junit本身是不支持普通的多线程测试的，这是因为Junit的底层实现上，是用System.exit退出用例执行的。JVM都终止了，在测试线程启动的其他线程自然也无法执行。JunitCore代码如下：&nbsp;</p>
<p>
	&nbsp;</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
/**

* Run the tests contained in the classes named in the &lt;code&gt;args&lt;/code&gt;.

* If all tests run successfully, exit with a status of 0. Otherwise exit with a status of 1.

* Write feedback while tests are running and write

* stack traces for all failed tests after the tests all complete.

* @param args names of classes in which to find tests to run

*/

public static void main(String... args) {

runMainAndExit(new RealSystem(), args);

}

/**

* Do not use. Testing purposes only.

* @param system

*/

public static void runMainAndExit(JUnitSystem system, String... args) {

Result result= new JUnitCore().runMain(system, args);

system.exit(result.wasSuccessful() ? 0 : 1);

}
</pre>
<div>
	<div>
		RealSystem：</div>
	<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
public void exit(int code) {

System.exit(code);

}
</pre>
	<div>
		<div>
			所以要想编写多线程Junit测试用例，就必须让主线程等待所有子线程执行完成后再退出。想到的办法自然是Thread中的join方法。话又说回来，这样一个简单而又典型的需求，难道会没有第三方的包支持么？通过google，笔者很快就找到了GroboUtils这个Junit多线程测试的开源的第三方的工具包。
			<div>
				&nbsp;</div>
			<div>
				&nbsp;GroboUtils官网如下：</div>
			<div>
				<span style="color: #0000ff;"><a href="http://groboutils.sourceforge.net/"><span style="color: #0000ff;">http://groboutils.sourceforge.net/</span></a></span></div>
			<div>
				&nbsp;</div>
			<div>
				下载页面：</div>
			<span style="color: #0000ff;"><a href="http://groboutils.sourceforge.net/downloads.html"><span style="color: #0000ff;">http://groboutils.sourceforge.net/downloads.html</span></a></span>
			<div>
				&nbsp;</div>
			<div>
				Maven依赖方式：</div>
			<div>
				<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
&lt;dependency&gt;

&lt;groupId&gt;net.sourceforge.groboutils&lt;/groupId&gt;

&lt;artifactId&gt;groboutils-core&lt;/artifactId&gt;

&lt;version&gt;5&lt;/version&gt;

&lt;/dependency&gt;
</pre>
			</div>
			<div>
				&nbsp;</div>
			<div>
				注：需要第三方库支持：</div>
			<div>
				<pre>
<span style="color: #0000ff;">Repository url https://oss.sonatype.org/content/repositories/opensymphony-releases</span></pre>
			</div>
		</div>
		<div>
			依赖好Jar包后就可以编写多线程测试用例了。上手很简单：</div>
		<div>
			<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
/**

* 多线程测试用例

*

* @author lihzh(One Coder)

* @date 2012-6-12 下午9:18:11

* @blog http://www.coderli.com

*/

@Test

public void MultiRequestsTest() {

// 构造一个Runner

TestRunnable runner = new TestRunnable() {

@Override

public void runTest() throws Throwable {

// 测试内容

}

};

int runnerCount = 100;

//Rnner数组，想当于并发多少个。

TestRunnable[] trs = new TestRunnable[runnerCount];

for (int i = 0; i &lt; runnerCount; i++) {

trs[i] = runner;

}

// 用于执行多线程测试用例的Runner，将前面定义的单个Runner组成的数组传入

MultiThreadedTestRunner mttr = new MultiThreadedTestRunner(trs);

try {

// 开发并发执行数组里定义的内容

mttr.runTestRunnables();

} catch (Throwable e) {

e.printStackTrace();

}

}
</pre>
		</div>
		<div>
			&nbsp;</div>
		<div>
			执行一下，看看效果。怎么样，你的Junit也可以执行多线程测试用例了吧：）。</div>
	</div>
</div>

