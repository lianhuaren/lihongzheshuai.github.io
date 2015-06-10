---
layout: post
title: 30岁码农从零单排iOS 第一季 5. 类
date: 2014-05-28 23:59
author: onecoder
comments: true
categories: [iOS, iOS基础, iOS教程, objective-c基础, objective-c教程]
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
	迫不及待进入类的世界。之前练习的时候就知道，接口和类不可能总和main函数写在一个文件里，迟早要分开。现在终于让我掌握了这个技巧。</p>
<p>
	File-&gt;new File&hellip;</p>
<p style="text-align: center;">
	<img alt="" src="http://onecoder.qiniudn.com/8wuliao/DN3jR5TW/z8Tvo.jpg" style="width: 700px; height: 476px;" /></p>
<p>
	选择Cocoa Touch Objective-C class</p>
<p style="text-align: center;">
	<img alt="" src="http://onecoder.qiniudn.com/8wuliao/DN3k5I07/qFgSk.jpg" style="width: 700px; height: 425px;" /></p>
<p>
	输入类名Fraction，自动生成两个文件Fraction.h(接口)和Fraction.m(实现类)，就是屌。</p>
<p>
	<br />
	main类里引用方式：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
#import &lt;Foundation/Foundation.h&gt;
#import &quot;Fraction.h&quot;</pre>
<p>
	引用自己的类用双引号&ldquo;&rdquo;，引用系统提供的用&lt;&gt;。区别对待。与Java屌，一视同仁的观点完全不同。</p>
<p>
	粘回之前的代码，运行方式不变。这里多学了一手。命令行编译：</p>
<pre class="brush:shell;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
clang -fobjc-arc -framework Foundation Fraction.m main.m -o Fraction</pre>
<p>
	生成一个Fraction文件，用可直接运行。屌。</p>
<p>
	接下来还有更屌的。什么get，set方法自动生成。iOS告诉你什么叫不用生成。接口里用@property声明属性，实现类里用@synthesize标记使用。就等于有了get和set方法。吊炸天。</p>
<p>
	接口变为：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
//
//  Fraction.h
//  ChapterSeven
//
//  Created by OneCoder on 14-5-26.
//  Copyright (c) 2014年 OneCoder. All rights reserved.
//

#import &lt;Foundation/Foundation.h&gt;

@interface Fraction : NSObject

@property int numerator, denominator;
-(void) print;
-(double) convertToNum;
@end
</pre>
<p>
	实现类变为：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
//
//  Fraction.m
//  ChapterSeven
//
//  Created by OneCoder on 14-5-26.
//  Copyright (c) 2014年 OneCoder. All rights reserved.
//

#import &quot;Fraction.h&quot;

@implementation Fraction

@synthesize numerator, denominator;

-(void) print{
    NSLog(@&quot;该分数的输出结果为: %i/%i&quot;, numerator, denominator);
}

-(double) convertToNum {
    return numerator / denominator;
}
@end
</pre>
<p>
	调用类不变，即相当于实现类里有setNumerator，setDenominator和numerator, denominator四个方法。恩，舒服。</p>
<p>
	当一个方法多参数的时候，又见高大上，可以给参数起名。作为Java屌，一开始还真没太反应过来。为什么又多了一个名字。直接看代码来体会吧：</p>
<p>
	接口声明：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
- (void) setNumerator:(int) num overDenominator:(int) d;
</pre>
<p>
	实现类：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
- (void) setNumerator:(int) n overDenominator :(int) d {
    numerator = n;
    denominator = d;
}
</pre>
<p>
	调用：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
  // 输入1/3
        [myFraction setNumerator: 1 overDenominator: 3];
</pre>
<p>
	&nbsp; &nbsp; &nbsp; 看了调用就会发现，这种方式看起来好易懂。否则像Java那样setXXX(1,3)，也不是1和3哪个是分子，哪个是分母。另外，OneCoder这里感觉还有一个原因就是，在Java里，你可以:</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
setNumber(int numerator, int denominator) {
     this.numerator = numerator;
     this.denominator = denominator;
}
</pre>
<p>
	但是Objective-C里，不支持这个两个变量名字不能一样。那这样可能就有机会更清晰一点。当然，你不写参数名也可以的。即变成：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
- (void) setNumber : (int) n : (int) d;
</pre>
<p>
	和</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
- (void) setNumber:(int)n :(int)d {
    numerator = n;
    denominator =d;
}
</pre>
<p>
	调用：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
// 输入3/7
        [myFraction setNumber:3 :7];&nbsp;</pre>
<p>
	果然就不清晰了。高端。</p>
<p>
	到目前为止，方法的参数和返回值都是基本数据类型。自然也是可以返回对象的。定义一个分数加法(接口声明略)：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
-(Fraction *) add:(Fraction *) f {
   
    numerator = numerator * f.denominator + denominator * f.numerator;
    denominator = denominator * f.denominator;
    return self;
   
}
</pre>
<p>
	可以看到参数和返回着都是Fraction类型。这里还有几个攻略里提到的技巧：</p>
<blockquote>
	<p>
		<br />
		&nbsp; 1. 点运算符，可以像Java一样用&rdquo;.&ldquo; 来调用方法。不过推荐的方式还是[]。这里OneCoder也不是很清楚，有参数的方法用点怎么调用。还是不能？？总之常规出装是[]就对了。<br />
		&nbsp; 2. self关键字。这个OneCoder看攻略半天没弄明白，一看代码，原来就是tm Java里的this。换个名字一下就大气了。</p>
</blockquote>
<p>
	传值和传引用。这个好像也跟Java里差不多。</p>
<p>
	这期攻略内容实在是多啊。。对了还有一个static关键字。好像也不太一样。类里全局的static倒是没什么区别，但是Objective-C里好像还可以在方法里写static变量。能对所有调用该方法的对象有效。测试一下。。</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
-(int) count {
    static int count = 0;
    count++;
    return count;
}
</pre>
<p>
	调用方法：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
NSLog(@&quot;方法内static调用结果: %i&quot;, [myFraction count]);
        NSLog(@&quot;方法内static调用结果: %i&quot;, [frac2 count]);
</pre>
<p>
	结果：</p>
<blockquote>
	<p>
		2014-05-28 23:35:18.995 ChapterSeven[15886:303] 方法内static调用结果: 1<br />
		2014-05-28 23:35:18.995 ChapterSeven[15886:303] 方法内static调用结果: 2</p>
</blockquote>
<p>
	<br />
	一样可以在对象间使用。Java里不支持这样的语法。</p>
<p>
	这一章攻略好歹是撸完了。精疲力尽啊。。越往后越难了。。进入中分段了。。。估计一章要分几次撸了。。天梯要慢慢爬。。晚了。睡了。。。</p>

