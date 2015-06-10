---
layout: post
title: Java小故事 不舍得叫醒女孩的男孩 观察者模式
date: 2012-07-25 20:56
author: onecoder
comments: true
categories: [Java, Java基础, observer, 小故事]
---
<span style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">今天在梳理项目里一些老的代码逻辑和结构的时候，发现了一段观察者的代码。脑海里同时浮现出这样一个故事&hellip;&hellip;</span>
<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
	&nbsp;</div>
<blockquote>
	<div style="font-family: Tahoma; font-size: 14px; text-align: -webkit-auto; ">
		有一个女孩和一个男孩，他们在山里迷路了。晚上，他们都精疲力尽。男孩对女孩说，你睡会吧，我看着，有动静我叫你，然而&hellip;&hellip;</div>
</blockquote>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
/**
 * Java小故事，不舍得叫醒女孩的男孩
 * &lt;p&gt;
 * 有一个女孩和一个男孩，他们在山里迷路了。&lt;br&gt;
 * 晚上，他们都精疲力尽。男孩对女孩说，你睡会吧，我看着，有动静我叫你。&lt;br&gt;
 * 然而&hellip;&hellip;
 * 
 * @author lihzh
 * @alia OneCoder
 * @blog http://www.coderli.com
 */
public class ObserverBoyAndGirlMain {

	/**
	 * @author lihzh
	 * @alia OneCoder
	 */
	public static void main(String[] args) {
		Boy boy = new Boy();
		Girl girl = new Girl();
		// 让女孩监听男孩的消息
		boy.addObserver(girl);
		try {
			// 然而，八个小时过去了，男孩的腿虽然已经被压得麻木了。
			Thread.sleep(8 * 60 * 60 * 1000L);
			// 但是，你舍得叫醒她么？：）
			// boy.wakeupGirl();
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
	}

	/**
	 * 男孩，观察周围的动静，有问题发出信号
	 * 
	 * @author lihzh
	 * @alia OneCoder
	 */
	public static class Boy extends Observable {
		/**
		 * 叫醒女孩
		 * 
		 * @author lihzh
		 * @alia OneCoder
		 */
		public void wakeupGirl() {
			setChanged();
			notifyObservers(&quot;&quot;);
		}
	}

	/**
	 * 女孩，睡着了，如果男孩叫她，则会醒来
	 * 
	 * @author lihzh
	 * @alia OneCoder
	 */
	public static class Girl implements Observer {
		@Override
		public void update(Observable o, Object arg) {
			System.out.println(&quot;Thank you, I&#39;m up.&quot;);
		}
	}
}
</pre>

