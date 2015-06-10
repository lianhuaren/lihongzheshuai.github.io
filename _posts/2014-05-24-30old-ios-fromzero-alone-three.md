---
layout: post
title: 30岁码农从零单排iOS 第一季 3. 数据类型和表达式
date: 2014-05-24 20:43
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
	第三期单排，比上一次来的更晚了一些。因为要给shurnim-storage项目写readme文档。终于算暂时告一段落。废话不多说，单排开始。</p>
<p>
	攻略第四章，介绍Objective-C中的数据类型。这里不再重复罗列书中内容。基本数据类型分几类:int, float, char和id(攻略说第9章再说，所以现在我也不懂。) 前三种，作为Java屌，我自然是懂的。整数，小数和字符。但是Objective-C中多了很多限定词: long, long long, short, unsigned singed。数据类型一下就扩展了。比如：</p>
<pre class="brush:cpp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
long int number
</pre>
<p>
	就相当于Java中的long一样，比int的范围更大了。但是这个</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
long long int number
</pre>
<p>
	真是好耿直的名字。含义也耿直，更大范围的int。当然，对于float来说，更大范围的double也还是有的。</p>
<p>
	进制方面。0开头表示八进制，0X表示16进制。屌丝，土豪一个味。</p>
<p>
	不过，之前我们知道用NSLog函数输入字符的时候，可以做变量的替换。%i 代表整型变量。自然这么多种数据类型，都有起对应的变量替换字符。如%li 对应 long int, %lli 对应 longlongint。。。。耿直到底。</p>
<p>
	有了数字，接下来就是运算。+-*/ 少不了，自然涉及到运算符的优先级。纵使你iOS在高大上，你也不能先+-后*/，括号也得优先搞。简言之，就是还是跟Java屌一个味。对了，差点忘记了%，模运算符，更*/一个优先级的。</p>
<p>
	说了这么多，虽觉无点可撸，但又觉不能只看不撸。综合考虑，撸一段类型转换包含简单运算的代码吧：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
//
//  main.m
//  ChapterFour
//
//  Created by OneCoder(Blog: http://www.coderli.com ) on 14-5-23.
//  Copyright (c) 2014年 OneCoder. All rights reserved.
//

#import &lt;Foundation/Foundation.h&gt;

int main(int argc, const char * argv[])
{
   
    @autoreleasepool {
        int i1, i2 = -150;
        float f1 = 123.124, f2;
        // 浮点转整型
        i1 = f1;
        NSLog(@&quot;%f 转换成整型后，值为: %i。&quot;,f1, i1);
        // 整型转浮点型
        f2 = i2;
        NSLog(@&quot;%i 转换成浮点型后，值为: %f。&quot;, i1, f2);
        // 整数除以整数
        f1 = i2/ 100;
        NSLog(@&quot;%i / 100 后赋值给浮点数，结果为: %f&quot;, i2, f1);
        // 整数除以浮点数
        f2 = i2 /100.0;
        NSLog(@&quot;%i / 100.0 后赋值给浮点数，结果为: %f&quot;, i2, f2);
        // 强转后运算
        f2 = (float) i2 / 100;
        NSLog(@&quot;将 %i 强转成float后，除以 100的计算结果为: %f&quot;, i2, f2);
    }
    return 0;
}

</pre>
<p>
	菊痒做了个小测试，就是将NSLog(@&quot;%i / 100.0 后赋值给浮点数，结果为: %f&quot;, i2, f2);<br />
	中的%f换成%i，看看什么效果。结果就是有警告，能编译通过，能执行，返回结果是奇怪的数字。</p>
<p>
	正常执行结果:</p>
<blockquote>
	<p>
		2014-05-24 14:03:33.374 ChapterFour[8785:303] 123.124001 转换成整型后，值为: 123。<br />
		2014-05-24 14:03:33.375 ChapterFour[8785:303] 123 转换成浮点型后，值为: -150.000000。<br />
		2014-05-24 14:03:33.375 ChapterFour[8785:303] -150 / 100 后赋值给浮点数，结果为: -1.000000<br />
		2014-05-24 14:03:33.376 ChapterFour[8785:303] -150 / 100.0 后赋值给浮点数，结果为: -1.500000<br />
		2014-05-24 14:03:33.376 ChapterFour[8785:303] 将 -150 强转成float后，除以 100的计算结果为: -1.500000<br />
		Program ended with exit code: 0</p>
</blockquote>
<p>
	改错后的执行结果：</p>
<blockquote>
	<p>
		2014-05-24 14:09:25.502 ChapterFour[8823:303] -150 / 100.0 后赋值给浮点数，结果为: -1583242847</p>
</blockquote>
<p>
	还有一点，Java里。150转成float输出是150.0，这里加了一堆0，可能为彰显霸气。测漏了。</p>
<p>
	学了数字类型，有运算符，不撸一个计算器出来，感觉都有点没脸贱人了。正好攻略上有样例，而且好几个习题跟相关，一并撸了，就当练手：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
/* 计算机类，可以进行加、减、乘和除运算。
可以累加结果。可以清空结果。完成时，返回最后计算结果。
修改：
实现4.5习题中
第8题，加减乘除返回累加结果功能。
第9题，添加三个方法
为什么没有第10题？
因为，读了半天没读懂。
*/
@interface Calculator : NSObject
//设置初始值
- (void) setInitValue : (double) i;
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
// 改变结果正负号
- (double) changeSign;
// 累加器的平方
- (double) xSquared;
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
    return result /= n;
}


- (void) setInitValue:(double)i {
    result = i;
}

- (void) clear {
    result = 0;
}

- (double) getResult {
    return result;
}

- (double) changeSign {
    return -result;
}

- (double) xSquared {
    return result * result;
}
@end
</pre>
<p>
	调用计算器代码：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
// 调用计算器
        Calculator *calculator = [Calculator new];
        [calculator setInitValue:1.0];
        [calculator multiply: 10];
        [calculator divide: 2];
        [calculator add : 3];
        [calculator subtract: 4];
        NSLog(@&quot; 1 * 10 / 2 + 3 - 4 = %f&quot;, [calculator getResult]);
        NSLog(@&quot;给结果改变符号后为: %f&quot;, [calculator changeSign]);
        NSLog(@&quot;结果的平方为: %f&quot;, [calculator xSquared]);
</pre>
<p>
	&nbsp; &nbsp; &nbsp; &nbsp;撸的时候有个小插曲，由于目前本屌只会把所有代码写在一个类里。所以，我自然顺手的就把节后和类追加在了main函数的后面，结果就是调用的时候找不到。原来，是有顺序的。。。gg。。</p>

