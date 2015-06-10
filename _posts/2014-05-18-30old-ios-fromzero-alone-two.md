---
layout: post
title: 30岁码农从零单排iOS 第一季 2. 类，对象和方法
date: 2014-05-18 23:15
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
	准备就绪，单排开始。<br />
	第一期单排几乎没有交流，不符合DOTA大神们单排时候的风格。 这期开始，配上解说。</p>
<p>
	攻略(《Objective-C程序设计》)第三章，讲的是面向对象的思想。这对于Java屌的OneCoder来说，毫无压力。正可谓，纵使你大iOS再高，再大，再上。Java屌也可以与其拥有共同的思想&mdash;&mdash;面向对象。</p>
<p>
	既然是面向对象，就少不了类和接口。先撸一段攻略里代码，分数处理程序，熟悉一下Objective-C的语法。</p>
<pre class="brush:cpp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
//
//  main.m
//  ChapterThree
//
//  Created by OneCoder on 14-5-12.
//  Blog http://www.coderli.com
//  Copyright (c) 2014年 OneCoder. All rights reserved.
//
#import &lt;Foundation/Foundation.h&gt;

// 分数处理程序

// 定义接口
@interface Fraction : NSObject

-(void) print;
-(void) setNumerator: (int) n;
-(void) setDenominator: (int) d;


@end

// 定义实现类

@implementation Fraction

{
    int numerator;
    int denominator;
}

-(void) print{
    NSLog(@&quot;该分数的输出结果为: %i/%i&quot;, numerator, denominator);
}

-(void) setNumerator :(int) n {
    numerator = n;
}

- (void) setDenominator:(int) d {
    denominator = d;
}

@end

// 调用接口
int main(int argc, const char * argv[])
{
   
    @autoreleasepool {
        Fraction *myFraction = [[Fraction alloc] init];
        Fraction *frac2 = [[Fraction alloc] init];
        // 输入1/3
        [myFraction setNumerator:1];
        [myFraction setDenominator:3];
        // 输出
        [myFraction print];
        // 输入3/7
        [frac2 setNumerator:3];
        [frac2 setDenominator:7];
        // 输出
        [frac2 print];
   
    }
    return 0;
}

</pre>
<p>
	@interface为接口声明。后跟接口名Fraction，冒号后为集成的基类NSObject.<br />
	第一个@end即为接口结束。<br />
	方法声明格式</p>
<blockquote>
	<p>
		-&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;（void）&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; setNumerator&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; :&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; (int)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; n;<br />
		方法类型&nbsp; 返回类型&nbsp;&nbsp;&nbsp;&nbsp; 方法名&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 方法有参数&nbsp;&nbsp;&nbsp; 参数类型&nbsp;&nbsp;&nbsp; 参数名称</p>
</blockquote>
<p>
	<br />
	-号代表实例方法。此外只有+号 表示类方法。</p>
<p>
	@implementation为实现类声明。实现接口声明中声明的方法。实现类名与接口声明相同。</p>
<p>
	<br />
	最后就是main函数的接口调用部分。</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
Fraction *myFraction = [[Fraction alloc] init];
</pre>
<p>
	声明并实例化一个Fraction类型的对象。</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
[[Fraction alloc] init];
</pre>
<p>
	是两段调用的缩写。完整的为：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
//分配内存存储空间
myFraction = [Fraction alloc];
// 初始化
myFraction = [myFraction init];
</pre>
<p>
	另外，变量起名得有规矩：</p>
<p>
	<br />
	必须以字母或者下划线(_)开后，只有可以是任何大小写字母，下划线或者0～9的数字。当然，不能是保留字。（大神用过的的ID自然不能使用。）</p>
<p>
	<br />
	有趣的是，所有声明和实现都可以写在一个文件里。</p>
<p>
	<br />
	熟悉了规矩，有了攻略傍体，自然要检验一下自己的功力。课后练习小撸一发。3.9,第7题。</p>
<pre class="brush:cpp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
// 习题7 笛卡尔坐标

@interface Descartes : NSObject
- (void) setXPoint : (int) x;
- (void) setYPoint : (int) y;
- (int) getXPoint;
- (int) getYPoint;
@end

@implementation Descartes {
    int X;
    int Y;
   
}

-(void) setXPoint:(int)x {
    X = x;
}
-(void) setYPoint:(int)y {
    Y = y;
}

-(int) getXPoint {
    return X;
}

-(int) getYPoint {
    return Y;
}

@end


// 习题3.7 调用
        Descartes *descartes = [Descartes new];
        [descartes setXPoint:10];
        [descartes setYPoint:15];
        NSLog(@&quot;笛卡尔坐标，X＝%i, Y=%i&quot;, [descartes getXPoint], [descartes getYPoint]);

</pre>
<p>
	题外话：</p>
<p>
	有了Java屌护体，感觉高大上的iOS似乎离我也不是那么的遥远。信心倍增。最近的生活喜忧参半，从零单排的不仅仅是iOS了，OneCoder现在的整个生活都是单排了。不过跟iOS一样这都是暂时的，熬过菜逼阶段，就可以和高端的队友一起虐菜了。</p>
<p>
	今天就到这了，一起期待第三期吧。</p>

