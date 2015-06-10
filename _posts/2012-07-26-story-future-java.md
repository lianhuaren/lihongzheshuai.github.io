---
layout: post
title: Java小故事 我许你一个未来 Future
date: 2012-07-26 21:41
author: onecoder
comments: true
categories: [future, Java, Java基础, 小故事]
---
还是梳理代码，频频到Future这个字眼，很自然的让我想到了未来&hellip;&hellip;<br />
<br />
<blockquote>
	<p>
		还是那对男孩和女孩，女孩问男孩，你会娶我吗？男孩说，一定会，等我为你盖好一栋美丽的房子&hellip;&hellip;</p>
</blockquote>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
/**
 * Java小故事 我许你一个未来 Future
 * &lt;p&gt;
 * 还是那对男孩和女孩，女孩问男孩，你会娶我吗？&lt;br&gt;
 * 男孩说，一定会，等我为你盖好一栋美丽的房子&hellip;&hellip;
 * 
 * @author lihzh
 * @alia OneCoder
 * @Blog http://www.coderli.com
 * @date 2012-7-26 下午8:44:58
 */
public class PromiseUAFuture {

	/**
	 * @author lihzh
	 * @throws ExecutionException
	 * @throws InterruptedException
	 * @alia OneCoder
	 * @date 2012-7-26 下午8:44:58
	 */
	public static void main(String[] args) throws InterruptedException, ExecutionException {
		// 我许你一个未来
		Future&lt;BeautifulHouse&gt; future = Executors.newSingleThreadExecutor().submit(new Boy());
		while (!future.isDone()) {
			System.out.println(&quot;Sorry baby, the house is not ok.&quot;);
		}
		System.out.println(future.get());
	}

	public static class Boy implements Callable&lt;BeautifulHouse&gt; {
		@Override
		public BeautifulHouse call() throws Exception {
			Thread.sleep(5000);
			return new BeautifulHouse();
		}

	}

	public static class BeautifulHouse {
		@Override
		public String toString() {
			return &quot;This is a beautiful house.&quot;;
		}
	}
}
</pre>

