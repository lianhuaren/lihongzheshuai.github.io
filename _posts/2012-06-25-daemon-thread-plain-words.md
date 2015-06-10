---
layout: post
title: 白话Java - 守护线程
date: 2012-06-25 22:47
author: onecoder
comments: true
categories: [Java, Java进阶, 守护线程, 白话]
---
<span style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">关于&ldquo;白话&rdquo;：偶然想到的词，也许有一天能成为一个系列。目的就是用简洁，明快的语言来告诉您，我所知道的一切。</span>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	&nbsp;</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	Java中的线程分两类，用户线程和守护线程。</div>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
Thread commonThread = new Thread(&quot;Common Thread&quot;);
</pre>
<span style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">这样就是用户线程。</span><br />
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
Thread daemonThread = new Thread(&quot;Daemon Thread&quot;);
daemonThread.setDaemon(true);
</pre>
<div class="Code">
	<span style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">这样就是守护线程。</span><br />
	<br />
	<span style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">起了&ldquo;守护&rdquo;线程这么动听的名字，自然要起到&ldquo;守护&rdquo;的作用。就好比男人要守护妹子。</span>
	<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
		守护线程的作用，按照网上的说法是：<span style="font-family: Arial; line-height: 26px; text-indent: 28px; "><font color="#ff0000">守护线程则是用来服务用户线程的，如果没有其他用户线程在运行，那么就没有可服务对象，也就没有理由继续下去。</font></span></div>
	<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
		<div>
			&nbsp;</div>
		<div>
			说白了就是妹子没了，男人也就自尽了。分情况写几个例子，一跑便知。<br />
			&nbsp;</div>
		<ul>
			<li>
				<span style="text-align: -webkit-auto; ">两个妹子 - 互不想干，你挂你的，我挂我的</span></li>
		</ul>
		<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
**
	 * 测试两个用户线程的情况
	 * 
	 * @author lihzh(OneCoder)
	 * @date 2012-6-25 下午10:07:16
	 */
	private static void twoCommonThread() {
		String girlOneName = &quot;Girl One&quot;;
		Thread girlOne = new Thread(new MyRunner(3000, girlOneName), girlOneName);
		String girlTwoName = &quot;Girl Two&quot;;
		Thread girlTwo = new Thread(new MyRunner(5000, girlTwoName), girlTwoName);
		girlOne.start();
		System.out.println(girlOneName + &quot;is starting.&quot;);
		girlTwo.start();
		System.out.println(girlTwoName + &quot;is starting&quot;);
	}

	private static class MyRunner implements Runnable {
		
		private long sleepPeriod;
		private String threadName;
		
		public MyRunner(long sleepPeriod, String threadName) {
			this.sleepPeriod = sleepPeriod;
			this.threadName = threadName;
		}
		@Override
		public void run() {
			try {
				Thread.sleep(sleepPeriod);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
			System.out.println(threadName + &quot; has finished.&quot;);
		}
	}
</pre>
	</div>
</div>
<img alt="" src="http://www.coderli.com/wp-content/uploads/2012/06/two-girls-live.png" style="width: 633px; height: 199px; " /><br />
<br />
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	开始都活着。<br />
	<br />
	<img alt="" src="http://www.coderli.com/wp-content/uploads/2012/06/two-girls-one-dead.png" style="width: 633px; height: 199px; " /><br />
	<br />
	<div style="text-align: -webkit-auto; ">
		3秒后，妹子1挂了，妹子2活的好好的，她的寿命是5秒。<br />
		&nbsp;</div>
	<ul>
		<li style="text-align: -webkit-auto; ">
			<span style="text-align: -webkit-auto; ">一妹子一王子</span></li>
	</ul>
	<div style="text-align: -webkit-auto; ">
		<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
      /**
	 * 测试一个用户一个守护线程
	 * 
	 * @author lihzh(OneCoder)
	 * @date 2012-6-25 下午10:22:58
	 */
	private static void oneCommonOneDaemonThread() {
		String girlName = &quot;Girl&quot;;
		Thread girl = new Thread(new MyRunner(3000, girlName), girlName);
		String princeName = &quot;Prince&quot;;
		Thread prince = new Thread(new MyRunner(5000, princeName), princeName);
		girl.start();
		System.out.println(girlName + &quot;is starting.&quot;);
		prince.setDaemon(true);
		prince.start();
		System.out.println(prince + &quot;is starting&quot;);
	}

</pre>
	</div>
</div>
<img alt="" src="http://www.coderli.com/wp-content/uploads/2012/06/one-girl-one-prince-live.png" style="width: 633px; height: 199px; " /><br />
<br />
<span style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">开始快乐的生活着，妹子能活3秒，王子本来能活5秒<br />
<br />
<img alt="" src="http://www.coderli.com/wp-content/uploads/2012/06/one-girl-one-prince-one-dead.png" /></span><br />
<br />
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	但是3秒后，妹子挂了，王子也殉情了。</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	&nbsp;</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	你可能会问，如果王子活3秒，妹子能活5秒呢。我只能说，虽然你是王子，该挂也得挂，妹子还会找到其他高富帅的，懂？<br />
	<br />
	<img alt="" src="http://www.coderli.com/wp-content/uploads/2012/06/prince-dead.png" /><br />
	<br />
	<span style="text-align: -webkit-auto; ">看，王子已经挂了。</span><br />
	&nbsp;</div>
<ul>
	<li style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
		<span style="text-align: -webkit-auto; ">两个王子</span></li>
</ul>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
        /**
	 * 测试两个守护线程
	 * 
	 * @author lihzh(OneCoder)
	 * @date 2012-6-25 下午10:29:18
	 */
	private static void twoDaemonThread() {
		String princeOneName = &quot;Prince One&quot;;
		Thread princeOne = new Thread(new MyRunner(5000, princeOneName), princeOneName);
		String princeTwoName = &quot;Prince Two&quot;;
		Thread princeTwo = new Thread(new MyRunner(3000, princeTwoName), princeTwoName);
		princeOne.setDaemon(true);
		princeOne.start();
		System.out.println(princeOneName + &quot;is starting.&quot;);
		princeTwo.setDaemon(true);
		princeTwo.start();
		System.out.println(princeTwoName + &quot;is starting&quot;);
	}

</pre>
</div>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	<img alt="" src="http://www.coderli.com/wp-content/uploads/2012/06/two-prince.png" /><br />
	<br />
	<div style="text-align: -webkit-auto; ">
		我只能说，没有妹子，没有活着的理由，直接就都挂了。</div>
	<div style="text-align: -webkit-auto; ">
		&nbsp;</div>
	<div style="text-align: -webkit-auto; ">
		现在，你懂守护线程了吗？</div>
</div>

