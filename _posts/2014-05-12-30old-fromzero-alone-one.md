---
layout: post
title: 30岁码农从零单排iOS 第一季 1.起步
date: 2014-05-12 23:00
author: onecoder
comments: true
categories: [iOS, iOS基础, iOS教程, ipad, iphone, objective-c基础, objective-c教程]
---
<blockquote>
	<p>
		什么是从零单排？DOTA大神们曾经流行的视频系列。不同的是，我是真正的从&ldquo;0&rdquo;单人自学，DOTA大神们是卖萌。<br />
		为什么从零单排？ 就想看看自己能不能坚持做好一件事情。<br />
		为什么是iOS？听起来高大上。移动互联时代，分杯翔也好。<br />
		为什么是30岁码农？因为30岁了，还是码农。<br />
		第一季单排什么？《Objective-C》程序设计(第4版)<br />
		既然是彩笔为什么还拿出来发？记录一个过程，给自己一个鞭策。顺便获得大家的批评，指正。</p>
</blockquote>
<p>
	&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;分割线&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;<br />
	单排开始，遵循攻略第2章，熟悉单排工具和基本操作(控制台输出)。<br />
	要想撸的爽，改建要做好。</p>
<p>
	<br />
	按照Java屌的Eclipse方式逐步修改常用快捷键。</p>
<p style="text-align: center;">
	<img alt="" src="http://onecoder.qiniudn.com/8wuliao/DKC4NNg7/l2fzj.jpg" style="width: 750px; height: 576px;" /></p>
<p>
	第一期撸过的代码如下：</p>
<pre class="brush:cpp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
//
//  main.m
//  FromZeroAlone
//
//  Created by OneCoder on 14-5-11.
//  Copyright (c) 2014年 OneCoder. All rights reserved.
//

#import &lt;Foundation/Foundation.h&gt;

int main(int argc, const char * argv[])
{
    @autoreleasepool {
        // 第一个Hello world程序。
        NSLog(@&quot;Hello, World!&quot;);
        /*
         Objective-C的注释
         好奇特的感觉
         */
        NSLog(@&quot;攻略说，这里加上@代表NSString字符串对象。&quot;);
        NSLog(@&quot;变量替换。\n 变量1: %i \n 变量2: %i \n 变量3: %i &quot;, 1, 2, 3);
        /*
         课后练习第四题
         */
        int a = 87;
        int b = 15;
        NSLog(@&quot;%i - %i = %i&quot;, a, b, (a - b));
       
    }
    return 0;
}
</pre>
<p>
	第一期，撸的没有什么技术含量，秒射，做个开端。</p>

