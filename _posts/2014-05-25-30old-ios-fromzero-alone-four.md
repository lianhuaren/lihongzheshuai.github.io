---
layout: post
title: 30岁码农从零单排iOS 第一季 4. 循环结构和控制结构
date: 2014-05-25 17:27
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
	&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;分割线&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;&mdash;</p>
<p>
	最近撸的节奏感很好，趁热打铁，一下撸两章。循环控制不分家。与屌Java几乎无区别。</p>
<p>
	<span style="color:#0000cd;"><em>循环</em>：</span><strong>for, while ,do while</strong>, 终止 <strong>break</strong>，继续下一轮: <strong>continue</strong>。<br />
	<span style="color:#0000cd;"><em>选择</em>:</span> &nbsp;<strong>if（else if）</strong>, <strong>switch</strong>, conditional（看完才知道就是三元运算）。</p>
<p>
	学习了两个新技能，从控制台输入scanf，布尔型变量: BOOL，值为YES和NO。(原来在上层社会，不用true和false啊。就是简洁。)</p>
<p>
	学会了一个小技巧，从控制台输入数据用scanf()，当读取字符的时候有一个小技巧，就是在格式化字符前加一个空格。如:<br />
	scanf(&ldquo; %c&rdquo;, &amp;c)<br />
	这样会使scanf在读取时跳过输入中的空白字符，如: 换行，制表，回车符。</p>
<p>
	由于是一些基本的操作，所以，综合撸一段代码，包涵上述所有元素。修改上一期撸过的计算器代码，这次要循环从控制台接收计算的表达式进行计算，支持+ - * /&nbsp; E操作。判断除数不能为0。E代表计算结束。(就跟真的一样。。。。)。废话说不动了，先撸为敬。</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
//
//  main.m
//  ChapterFiveSix
//
//  Created by OneCoder(Blog: http://www.coderli.com ) on 14-5-25.
//  Copyright (c) 2014年 OneCoder. All rights reserved.
//

#import &lt;Foundation/Foundation.h&gt;

/* 计算机类，可以进行加、减、乘和除运算。
可以累加结果。可以清空结果。完成时，返回最后计算结果。
*/
@interface Calculator : NSObject
//加法
- (double) add : (double) n;
//减法
- (double) subtract : (double) n;
//乘法
- (double) multiply : (double) n;
//除法
- (double) divide : (double) n;
//清零
- (void) clear;
//返回结果
- (double) getResult;
@end

@implementation Calculator
{
    double result;
}
- (double) add:(double) n {
    return result += n;
}

- (double) subtract:(double) n {
    return result -= n;
}

- (double) multiply:(double) n {
    return result *= n;
}

- (double) divide:(double) n {
    if (n == 0) {
        return NAN;
    }
    return result /= n;
}

- (void) clear {
    result = 0;
}

- (double) getResult {
    return result;
}

@end


int main(int argc, const char * argv[])
{
   
    @autoreleasepool {
        //从控制台输入数据
        char operator;
        double number;
        Calculator *calculator = [Calculator new];
        BOOL isCorrect = YES;
        do {
            NSLog(@&quot;请输入表达式。支持+ - * / E 操作&quot;);
            // 从控制台读取输入
            scanf(&quot; %c %lf&quot;, &amp;operator, &amp;number);
            switch (operator) {
                case &#39;+&#39;:
                    NSLog(@&quot; + %lf = %lf&quot;, number, [calculator add : number]);
                    break;
                case &#39;-&#39;:
                    NSLog(@&quot; - %lf = %lf&quot;, number, [calculator subtract : number]);
                    break;
                case &#39;*&#39;:
                    NSLog(@&quot; * %lf = %lf&quot;, number, [calculator multiply : number]);
                    break;
                case &#39;/&#39;:
                    NSLog(@&quot; / %lf = %lf&quot;, number, [calculator divide : number]);
                    break;
                case &#39;E&#39;:
                case &#39;e&#39;:
                    NSLog(@&quot;计算结束，最后结果为: %lf&quot;, [calculator getResult]);
                    break;
                default:
                    NSLog(@&quot;不支持的的操作类型: %c , 结束计算。&quot;, operator);
                    isCorrect = NO;
                    break;
            }
        } while(operator != &#39;E&#39; &amp;&amp; operator != &#39;e&#39; &amp;&amp; isCorrect);
    }
    return 0;
}

</pre>
<p>
	撸的过程中，不小心发现一个问题。就是那个BOOL类型的变量。可以写BOOL也可以写bool，值可以是YES/NO 也可以是TRUE/FALSE。</p>
<p>
	查了一下，了解个大概，</p>
<blockquote>
	<p>
		bool 是int<br />
		BOOL 是char</p>
	<p>
		bool b1 当b1&gt;0 就＝YES<br />
		BOOL b2 当b2=1 才＝YES</p>
</blockquote>
<p>
	所以框架都用BOOL。</p>
<p>
	<br />
	撸多了，累，这期到这，休息一下。下期就到类了～～<br />
	&nbsp;</p>

