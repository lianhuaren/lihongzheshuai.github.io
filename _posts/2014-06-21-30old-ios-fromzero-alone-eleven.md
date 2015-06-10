---
layout: post
title: 30岁码农从零单排iOS 第一季 11. 预处理
date: 2014-06-21 23:38
author: onecoder
comments: true
categories: [define, iOS, objective-c, 基础, 预处理]
---
<blockquote>
	<p>
		第一季单排什么？是OneCoder学习《Objective-C程序设计》(第4版)的经过和总结。</p>
	<p>
		今天心跳好快，以为要挂了&hellip;&hellip;</p>
</blockquote>
<p>
	预处理也是Java里没有的概念，个人理解就预先给一段话起个名字，以后就用这个名字来代替这段话。这里的意思指的是文本替换，而不是像给变量赋值那样。通过代码，来介绍宏的定义和使用:</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
//
//  main.m
//  ChapterTwelve
//
//  Created by OneCoder(Blog: http://www.coderli.com) on 14-6-21.
//  Copyright (c) 2014年 OneCoder. All rights reserved.
//
/*
预处理程序练习代码
*/
#import &lt;Foundation/Foundation.h&gt;

//定义一个BLOG的宏，代表OneCoder的blog地址
// 注意语法，中间不是等号，而是空格
#define BLOG &quot;http://www.coderli.com&quot;
// 注意，由于是文本替换，所以结尾不可以加分号;
// 否则，分号也将替换进去，从而可能产生语法错误。
// 下面的定义，充分证明了预定义实际执行的文本替换，并且是在编译期的。
// 注意看下面使用该宏的代码片段
#define AGE ,30
// 由于是文本替换，所以自然可以理解，宏定义中，可以是任何的表达式
// 并且宏之间可以相互引用，也可以接受参数。
#define PI 3.14
// r是宏的参数，动态变化，表达式里加括号，因为这里也是文本替换
// 识想，如果传递x+1给该宏，如果不加括号则变成PI * x + 1 * x + 1，结果自然错了。
#define AREA(r)  PI * (r) * (r)

int main(int argc, const char * argv[])
{
   
    @autoreleasepool {
        // 使用定义的宏
        NSLog(@&quot;OneCoder的博客地址是: %s&quot;, BLOG);
        // 使用AGE定义的例子，当然实际中没人会这么编码。
        NSLog(@&quot;年龄是: %i&quot; AGE);
        // 使用可穿参数的宏的样例
        int r = 10;
        NSLog(@&quot;面积是: %f&quot;, AREA(r));
        // 可以定义宏，我们自然可以判断是否定义了某宏
        //
#ifdef NAME
        NSLog(@&quot;定义了宏NAME。&quot;);
        NSLog(@&quot;输出名字: %s&quot;, BLOG);
# elif defined(NICKNAME) // 特殊运算符defined(name)，判断某宏是否定义。
        NSLog(@&quot;定义了小名: %s&quot;, NICKNAME);
#else
        NSLog(@&quot;没有定义名字，重新定义。&quot;);
#define NAME &quot;Yummy-OneCoder&quot;
        NSLog(@&quot;名字是: %s&quot;, NAME);
    }
#endif
    // 可以定义就可以取消定义
#undef NAME
#ifndef NAME
    NSLog(@&quot;宏NAME没有定义过。&quot;);
#endif
    return 0;
}&nbsp;</pre>
<p>
	输出如下：</p>
<blockquote>
	<p>
		2014-06-21 23:35:38.748 ChapterTwelve[29160:303] OneCoder的博客地址是: http://www.coderli.com<br />
		2014-06-21 23:35:38.749 ChapterTwelve[29160:303] 年龄是: 30<br />
		2014-06-21 23:35:38.750 ChapterTwelve[29160:303] 面积是: 314.000000<br />
		2014-06-21 23:35:38.750 ChapterTwelve[29160:303] 没有定义名字，重新定义。<br />
		2014-06-21 23:35:38.750 ChapterTwelve[29160:303] 名字是: Yummy-OneCoder<br />
		2014-06-21 23:35:38.750 ChapterTwelve[29160:303] 宏NAME没有定义过。<br />
		Program ended with exit code: 0</p>
</blockquote>
<p>
	<br />
	由此来看，宏的用户还是很多的。单独定义一下整个程序中需要使用的宏，方便统一的维护管理，增加代码的可读性。</p>

