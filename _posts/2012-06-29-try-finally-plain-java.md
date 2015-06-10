---
layout: post
title: 白话Java - try-finally块
date: 2012-06-29 11:45
author: onecoder
comments: true
categories: [finally, Java, Java基础, try]
---
<div style="font-family: Tahoma; font-size: 14px; ">
	本文讲述的是Java中try finally代码块执行顺序，和当其存在于循环中的时候的跳出和执行问题。白话，也要简化，一段代码，来说明问题： &nbsp; &nbsp;<br />
	<br />
	<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
  /**
	 * @author lihzh
	 * @date 2012-6-8 下午9:21:22
	 * @blog http://www.coderli.com
	 */
	public static void main(String[] args) {
		for (int i = 0; i &lt; 10; i++) {
			System.out.println(&quot;Begin loop: &quot; + i);
			if (i == 2) {
				System.out.println(&quot;Continue: &quot; + i);
				continue;
			}
			try {
				System.out.println(&quot;i = &quot; + i);
				if (i == 4) {
					System.out.println(&quot;In try continue: &quot; + i);
					continue;
				}
				if (i == 5) {
					return;
				}
			} finally {
				System.out.println(&quot;This is finally. &quot; + i);
			}
		}
	}

</pre>
</div>
<div style="font-family: Tahoma; font-size: 14px; ">
	执行结果：</div>
<div style="font-family: Tahoma; font-size: 14px; ">
	<pre class="brush:shell;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
Begin loop: 0
i = 0
This is finally. 0
Begin loop: 1
i = 1
This is finally. 1
Begin loop: 2
Continue: 2
Begin loop: 3
i = 3
This is finally. 3
Begin loop: 4
i = 4
In try continue: 4
This is finally. 4
Begin loop: 5
i = 5
This is finally. 5
</pre>
</div>
<span style="font-family: Tahoma; font-size: 14px; ">如果还没看明白，那我再啰嗦两句：finally就是最后的，跟try和catch是兄弟，就好像正常人走try分支，走着走着你不正常了，就开始走catch分支，但是不管你正常不正常，最后都要通过最终的检测，才算完。他们是一个组合。至于外面的for循环，就好像你在跑圈，本来每圈你都路过这个关卡(try-finnaly)，但是你说，第二圈(i=2)跑一半的时候，突然说，下面我不跑了，我要开始跑第三圈，那关卡也拿你没辙。拦不住你。至于跑到第五圈了，你说不行了，我跑不动了，以后都不跑了，那也行你退出了，不过你已经进入关卡了，一样，唯一的出口就在finally。通过再退出。</span>
