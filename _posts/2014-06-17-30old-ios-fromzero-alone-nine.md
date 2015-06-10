---
layout: post
title: 30岁码农从零单排iOS 第一季 9. 分类
date: 2014-06-17 00:29
author: onecoder
comments: true
categories: [iOS, iOS基础, iOS教程, objective-c]
---
<blockquote>
	<p>
		第一季单排什么？是OneCoder学习《Objective-C程序设计》(第4版)的经过和总结。</p>
	<p>
		世界杯&hellip;&hellip;节奏一下乱了&hellip;&hellip;努力找回&hellip;&hellip;</p>
</blockquote>
<hr />
<p>
	今天撸是攻略的第11章，概念较新，单纯从文字理解来看，读懂个4，5分，还是通过代码的验证来加深理解。老规矩，&ldquo;编打编录&rdquo;，把理解写在代码的注释中。</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
//
//  ClassA.h
//  ChapterEleven
//
//  Created by OneCoderr(Blog http://www.coderli.com ) on 14-6-16.
//  Copyright (c) 2014年 OneCoder. All rights reserved.
//

#import &lt;Foundation/Foundation.h&gt;

@interface ClassA : NSObject

- (void) methodOne;

@end


// 定义一个ClassA的分类，实际是对ClassA接口的一个扩展
// 定义方式为增加括号(分类名)
// 由于分类是对原有接口的扩展，所以无需在申明基类，同时，如果与原始接口不定义在一个文件中，则需要import原接口
@interface ClassA (catagoryA)

- (void) methodTwo;

@end


//
//  ClassA.m
//  ChapterEleven
//
//  Created by OneCoder(Blog http://www.coderli.com ) on 14-6-16.
//  Copyright (c) 2014年 OneCoder. All rights reserved.
//

#import &quot;ClassA.h&quot;

/*
在实现部分，可以一起实现所有分类中定义的方法，也可像下面这样指定分类，分别实现。
*/
@implementation ClassA

- (void) methodOne {
    NSLog(@&quot;这是方法One&quot;);
}

@end

@implementation ClassA (catagoryA)

- (void) methodTwo {
    NSLog(@&quot;这是分类中的方法Two&quot;);
}

@end</pre>
<p>
	调用部分：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
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
    }
    return 0;
}
</pre>
<p>
	这里OneCoder做了了一个测试，在分类中也可以实现原始接口中定义的方法，效果就是会复写之前的实现。不过攻略说，虽然分类可以实现这个效果，但是不推荐这么使用。是一种拙劣的设计，覆写应该用子类。</p>
<p>
	如果类的分类中不指定名字，称为类的扩展，是一种特殊情况。老规矩，测试一下，在上面接口定义上追加：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
//测试未命名分类，即类的扩展
// 允许扩展属性，指定名字的不可以
@interface ClassA ()

@property int uniqueID;

-(void) methodThree;

@end
</pre>
<p>
	实现类改为：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
//
//  ClassA.m
//  ChapterEleven
//
//  Created by OneCoder(Blog http://www.coderli.com ) on 14-6-16.
//  Copyright (c) 2014年 OneCoder. All rights reserved.
//

#import &quot;ClassA.h&quot;

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


@end

@implementation ClassA (catagoryA)

- (void) methodTwo {
    NSLog(@&quot;这是分类中的方法Two&quot;);
}


@end

</pre>
<p>
	如果将methodThree的实现放到下面分类的实现中，编译器会警告。</p>
<p>
	分类给了我们很好的在不修改已有类代码的情况下扩展原有类的机会。很应该好好理解。分类有一些注意事项：</p>
<blockquote>
	<p>
		1、可以拥有很多分类。如果一个方法定义在多个分类中，无法指定哪个分类。<br />
		2、使用分类扩展，会影响该类的所有子类。<br />
		3、类名(分类名)必须是全局唯一的。</p>
</blockquote>
<p>
	不说了，看德国vs葡萄牙了，支持德国。2：0，2：1，3：0都好。</p>
<p>
	&nbsp;</p>

