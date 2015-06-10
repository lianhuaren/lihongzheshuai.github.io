---
layout: post
title: 30岁码农从零单排iOS 第一季 10. 协议
date: 2014-06-19 22:33
author: onecoder
comments: true
categories: [iOS, objective-c, 代理, 协议, 非正式协议]
---
<blockquote>
	<p>
		第一季单排什么？是OneCoder学习《Objective-C程序设计》(第4版)的经过和总结。</p>
	<p>
		最近有点燥，需要安静一下了&hellip;&hellip;</p>
</blockquote>
<p>
	最近两期学的东西是以前没有接触过的，感觉挺新鲜。</p>
<blockquote>
	<p>
		什么是协议？<br />
		协议是多个类共享的一个方法列表。协议中列出的方法没有相应的实现，计划由其他人来实现。协议提供了一种方式，用指定的名称定义一组多少有点相关的方法。</p>
</blockquote>
<p>
	这里OneCoder理解有点类似于Java中实现多接口的效果。比如Java中的Cloneable接口和Serializable接口，如果类支持拷贝和序列化则需要在实现自己的接口的同时，额外实现这两个接口。这两个接口在OC中就可以定义成协议，这是OneCoder自己的理解，具体应用场景，还要随着学习的深入逐步研究。先看一段代码例子：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
//
//  ClassA.h
//  ChapterEleven
//
//  Created by OneCoderr(Blog http://www.coderli.com ) on 14-6-16.
//  Copyright (c) 2014年 OneCoder. All rights reserved.
//

#import &lt;Foundation/Foundation.h&gt;

//定义一个名为OneCoder的协议
@protocol OneCoder

//定义一个方法，输出博客地址
// required表明必须实现的方法
@required
- (void) printBlogAddr;
// optional表明可选实现的方法
@optional
- (void) test;
@end

@interface ClassA : NSObject &lt;OneCoder&gt;

- (void) methodOne;

@end
// 定义一个ClassA的分类，实际是对ClassA接口的一个扩展
// 定义方式为增加括号(分类名)
// 由于分类是对原有接口的扩展，所以无需在申明基类，同时，如果与原始接口不定义在一个文件中，则需要import原接口
@interface ClassA (catagoryA)

- (void) methodTwo;

@end

//测试未命名分类，即类的扩展
// 允许扩展属性，指定名字的不可以
@interface ClassA ()

@property int uniqueID;

-(void) methodThree;

@end

</pre>
<p>
	还是之前的ClassA接口，不过这里在开头用@protocol定义了一个协议，并在下面接口定义中用&lt;OneCoder&gt;方式，使用该协议，多个协议用,分隔。接口实现：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
/*
在实现部分，可以一起实现所有分类中定义的方法，也可像下面这样指定分类，分别实现。
*/
@implementation ClassA

@synthesize uniqueID;
- (void) methodOne {
    NSLog(@&quot;这是方法One&quot;);
}

-(void) methodThree {
    NSLog(@&quot;这是方法THree&quot;);
}

//实现了协议OneCoder中定义的方法
- (void) printBlogAddr {
    NSLog(@&quot;OneCoder的博客地址是: http://www.coderli.com&quot;);
}


使用方式：
//
//  main.m
//  ChapterEleven
//
//  Created by OneCoder on 14-6-16.
//  Copyright (c) 2014年 OneCoder. All rights reserved.
//

#import &lt;Foundation/Foundation.h&gt;
#import &quot;ClassA.h&quot;
int main(int argc, const char * argv[])
{

    @autoreleasepool {
       
        ClassA *cA = [[ClassA alloc] init];
        [cA methodOne];
        [cA methodTwo];
        [cA methodThree];
        [cA uniqueID];
        // 调用协议中定义的方法
        [cA printBlogAddr];
        // 判断是否支持协议OneCoder
        if ([cA conformsToProtocol: @protocol(OneCoder)]) {
            NSLog(@&quot;支持协议OneCoder&quot;);
        }
    }
    return 0;
}

</pre>
<p>
	就和直接调用接口中定义的方法一样。检查一个对象是否支持协议使用conformsToProtocol方法。</p>
<p>
	OneCoder通过阅读一个源码中使用协议的例子又进一步理解了一下协议的作用，比如，在一个类中有一个类型为id&lt;协议名&gt; 的属性，那么就可以在类中直接使用该对象所遵循的协议定义的方法，辅助实现该类的功能。</p>
<p>
	关于非正式协议：</p>
<p>
	不得不说，攻略从第十章开始，翻译的就难以理解，这里OneCoder从网上摘录一下一段话，感觉可以理解了：</p>
<blockquote>
	<p>
		一、非正式协议<br />
		显然这个名词是相对于正式协议而言的。在解释非正式协议之前，先引用两段话：<br />
		1、在《Cocoa设计模式》第六章类别的6.3.2把类别用于非正式协议一节中，这样写到：<br />
		非正式协议通常定义为NSObject的类别。类别接口中指定的方法可能会或者可能不会被框架类实际地实现。非正式协议位于一种设计灰区中。正式协议由编译器检查并且代表一种关于对象能力的保证，但是非正式协议不会做出保证----而只会给出提示。<br />
		2、苹果官方文档Cocoa Core Competencies一文中是这样介绍非正式协议的：<br />
		An informal protocol is a category on NSObject, which implicitly makes almost all objects adopters of the protocol. (A category is a language feature that enables you to add methods to a class without subclassing it.) Implementation of the methods in an informal protocol is optional. Before invoking a method, the calling object checks to see whether the target object implements it. Until optional protocol methods were introduced in Objective-C 2.0, informal protocols were essential to the way Foundation and AppKit classes implemented delegation.<br />
		大概意思：非正式协议是NSObject类（显而易见，还包括它的子类）的类别，其所有的子类都含蓄地接受了这个协议。（类别是Objective-C的一个语言特点，可以让你在无需子类化的前提下为一个类增加方法。）非正式协议中的方法是否实现都是可选的，因此在调用非正式协议中的方法之前，需要去检查对象类是否实现了它。在Objective-C2.0中引入可选的正式协议方法之前，非正式协议是Foundation和AppKit类实现委托的唯一方式。<br />
		3、到底什么是非正式协议<br />
		综合1、2两段引文可以看出，所谓的非正式协议就是类别，即凡是NSObject或其子类的类别，都是非正式协议。</p>
	<p>
		引自：http://blog.csdn.net/wzzvictory/article/details/9295317</p>
</blockquote>
<p>
	<br />
	<br />
	最后，补一段OneCoder理解的协议应用的例子，体会一下协议的作用：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
//
//  Coder.h
//  ChapterEleven
//
//  Created by OneCoder(Blog: http://www.coderli.com) on 14-6-19.
//  Copyright (c) 2014年 OneCoder. All rights reserved.
//

#import &lt;Foundation/Foundation.h&gt;

// 定义一个play协议
@protocol Play &lt;NSObject]]&gt;

- (void) doPlay;

@end
//定义一个码农接口，内含一个partner属性
// 该属性需要实现协议play，表明码农将要进行的娱乐活动。
// 定义一个play方法，代表开始游戏。
@interface Coder : NSObject

@property id&lt;Play&gt; partner;

- (void) play;

@end

// 定义男小伙伴接口，遵守协议Play
@interface Boy : NSObject&lt;Play&gt;


@end

// 定义女小伙伴接口，遵守协议Play
@interface Girl : NSObject&lt;Play&gt;


@end

</pre>
<p>
	使用方式：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
   Coder *coder = [[Coder alloc] init];
        Boy *b = [[Boy alloc] init];
        [coder setPartner:b];
        [coder play];
        Girl *g = [[Girl alloc] init];
        [coder setPartner: g];
        [coder play];

</pre>
<p>
	&nbsp; &nbsp; &nbsp;输出：</p>
<blockquote>
	<p>
		2014-06-19 22:27:14.433 ChapterEleven[28021:303] 我们是好基友，我们来赏菊吧。<br />
		2014-06-19 22:27:14.433 ChapterEleven[28021:303] 你是不是想多了，臭屌丝。<br />
		&nbsp;</p>
</blockquote>

