---
layout: post
title: 30岁码农从零单排iOS 第一季 8. 变量和数据类型（上）
date: 2014-06-11 23:06
author: onecoder
comments: true
categories: [iOS, iOS基础, iOS教程, objective-c基础, objective-c教程]
---
<blockquote>
	<p>
		第一季单排什么？是OneCoder学习《Objective-C程序设计》(第4版)的经过和总结。<br />
		上班感觉好累&hellip;&hellip;沙发编码感觉好爽&hellip;&hellip;攻略第十章的学习感觉和OneCoder上次翻译的Swift的官方文档的进度比较匹配。</p>
</blockquote>
<p>
	首先介绍的是构造函数，之前我们构造一个对象都是直接[类名 new]了一个，等于无参的，那么有参该怎么办，下面是答案：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
//
//  ClassA.h
//  ChapterTen
//
//  Created by OneCoder(Blog: http://www.coderli.com ) on 14-6-11.
//  Copyright (c) 2014年 OneCoder. All rights reserved.
//

#import &lt;Foundation/Foundation.h&gt;

@interface ClassA : NSObject

- (ClassA *) initWithANumber : (int) n;

@end

//
//  ClassA.m
//  ChapterTen
//
//  Created by OneCoder(Blog: http://www.coderli.com ) on 14-6-11.
//  Copyright (c) 2014年 OneCoder. All rights reserved.
//

#import &lt;Foundation/Foundation.h&gt;
#import &quot;ClassA.h&quot;

@implementation ClassA

- (ClassA *) initWithANumber: (int) n {
    self =[super init];
    NSLog(@&quot;这是一个初始化方法。%i&quot;, n);
    return self;
}

@end

//
//  main.m
//  ChapterTen
//
//  Created by OneCoder(Blog: http://www.coderli.com ) on 14-6-11.
//  Copyright (c) 2014年 OneCoder. All rights reserved.
//

#import &lt;Foundation/Foundation.h&gt;
#import &quot;ClassA.h&quot;

int main(int argc, const char * argv[])
{

    @autoreleasepool {
       
        ClassA *a = [[ClassA alloc] initWithANumber : (int) 3];
       
    }
    return 0;
}
</pre>
<p>
	比Java麻烦的多。<em>第一</em>，接口里也必须声明这个函数，<em>第二</em>，作为构造函数的方法必须以init+大写字母或者返回id类型，否则编译都会报错。之前看攻略里说，还以为只是推荐这样。巧了这里OneCoder正好写错了，结果就报错了。就是这么巧。</p>
<p>
	不过，对于可能会被继承的构造函数，建议返回id类型，而不是硬编码类型。因为子类对象并不等于父类对象。</p>
<p>
	<em>关于变量作用域</em></p>
<p>
	最开始OneCoder还以为OC里方法只有+和-号两种作用域，变量也差不都呢。结果原来OC里也有四种作用域：</p>
<blockquote>
	<p>
		@protected &mdash;&mdash; 可被当前类和子类中定义的方法访问，接口部分定义的实例变量默认作用域。<br />
		@private &mdash;&mdash; 只能被定义该变量的类的方法访问。在实现部分定义的实例变量默认是这种作用域。<br />
		@public &mdash;&mdash; 可以被所有类中方法访问。<br />
		@package &mdash;&mdash; 这个OneCoder根本没懂，转述攻略原话：对于64位映像，可以在实例该类的映像中的任何地方访问这个实例变量。</p>
</blockquote>
<p>
	测试一下：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
//
//  ClassA.m
//  ChapterTen
//
//  Created by OneCoder(Blog: http://www.coderli.com ) on 14-6-11.
//  Copyright (c) 2014年 OneCoder. All rights reserved.
//

#import &lt;Foundation/Foundation.h&gt;
#import &quot;ClassA.h&quot;

@implementation ClassA

{
@private
    int num;
@protected
    int abc;
}

- (ClassA *) initWithANumber: (int) n {
    self =[super init];
    self -&gt; num = n;
    NSLog(@&quot;这是一个初始化方法。%i&quot;, num);
    return self;
}
@end

//
//  main.m
//  ChapterTen
//
//  Created by OneCoder(Blog: http://www.coderli.com ) on 14-6-11.
//  Copyright (c) 2014年 OneCoder. All rights reserved.
//

#import &lt;Foundation/Foundation.h&gt;
#import &quot;ClassA.h&quot;

int main(int argc, const char * argv[])
{

    @autoreleasepool {
       
        ClassA *a = [[ClassA alloc] initWithANumber : 3];
       
    }
    return 0;
}

</pre>
<p>
	这里不小心通过代码提示，用了一个-&gt;运算符访问了实例变量。具体会在13章介绍。</p>
<p>
	不得不说，这种攻略感觉写的好晦涩啊。真是很难看懂，只能多做实验了。经过多方实验，OneCoder似乎理解了攻略中关于</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
@synthesize number=_number;
</pre>
<p>
	用法的含义。好像就是给这个属性起了一个别名。由于使用@synthesize方式声明的变量是private的，所以只能在当前类中使用。而且只能用名字_number来使用。真是反复看了多遍，难道是我迟钝么？</p>
<p>
	<strong>关于全局变量</strong></p>
<p>
	全局变量自然要声明在全局，就是类中所有方法的外部。</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
@implementation ClassA

int count = 16;

&hellip;
</pre>
<p>
	在本类中直接用变量名就可以访问，在别的类中，可以用extern关键字访问。</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
@autoreleasepool {
        extern int count;
        ClassA *a = [[ClassA alloc] initWithANumber : count];
       
    }
</pre>
<p>
	这个用起来到挺方便的。</p>
<p>
	<br />
	<strong>关于静态变量</strong></p>
<p>
	看例子：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
#import &lt;Foundation/Foundation.h&gt;
#import &quot;ClassA.h&quot;

@implementation ClassA

int count = 16;
static int initCount = 0;
@synthesize number=_number;


- (ClassA *) initWithANumber: (int) n {

    self =[super init];
    initCount++;
    NSLog(@&quot;这是一个初始化方法。%i, 初始化次数：%i&quot;, n, initCount);
    return self;
}

- (void) test {

    NSLog(@&quot;%i&quot;, initCount);
}
@end

</pre>
<p>
	这里OneCoder总结了一下(攻略里的话看起来实在太累了)，跟Java一样，声明在全局则所有方法都可以访问，且访问不需要通过类的实例。</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
 @autoreleasepool {
        extern int count;
        ClassA *a = [[ClassA alloc] initWithANumber : count];
        a = [[ClassA alloc] initWithANumber : count];
        a = [[ClassA alloc] initWithANumber : count];
       
    }

</pre>
<p>
	之前我们测试过，即使你定义在方法内，对该方法多次调用，值会记录，不会重新初始化。</p>
<p>
	&nbsp; &nbsp;三次初始化，输出1，2，3。即使你把initCount定义在方法内，也是一样。就是这个意思。</p>
<p>
	这章文字不多，看起来太费劲，感觉似乎是很简单的道理，不知道怎么翻译的那么蹩脚&hellip;&hellip;晚了，先写到这，明天在写下半部分，关于枚举，类型定义和位操作。</p>

