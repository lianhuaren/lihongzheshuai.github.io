---
layout: post
title: 30岁码农从零单排iOS 第一季 12. NSNumber和NSLog
date: 2014-06-28 13:36
author: onecoder
comments: true
categories: [iOS, nslog, nsnumber, objective-c]
---
<blockquote>
	<p>
		第一季单排什么？是OneCoder学习《Objective-C程序设计》(第4版)的经过和总结。</p>
	<p>
		又仿佛间隔的有点久了&hellip;&hellip;</p>
</blockquote>
<p>
	单排12期，不过这次到了15章。间隔久也是因为阅读了较长的13章，而无需演练的原因。</p>
<p>
	15章也很长，主要介绍了Foundation框架里的一些类。还是分段演练。老规律，以战代练：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
//
//  main.m
//  ChapterFifteen
//
//  Created by OneCoder(http://www.coderli.com) on 14-6-28.
//  Copyright (c) 2014年 OneCoder. All rights reserved.
//

#import &lt;Foundation/Foundation.h&gt;

int main(int argc, const char * argv[])
{
   
    @autoreleasepool {
        // NSNumber对象使用
        NSNumber *myNumber, *floatNumber, *intNumber;
        NSInteger myInteger;
        // 利用提供的构造函数来实例化对象，注意数据类型要对应
        // 值为整型
        intNumber = [NSNumber numberWithInt: 99];
        int i = [intNumber intValue];
        // 通过整型方法获取值
        NSLog(@&quot;整型值为:%i&quot;, i);
        // 此时尝试修改值，会报错，也就是说NSNumber中的值是不可修改的。
        //        myNumber = [myNumber initWithInt: 100];
//        i = [myNumber intValue];
        // 值为浮点型
        floatNumber = [NSNumber numberWithFloat:99.99];
        NSLog(@&quot;浮点型值为:%f&quot;, [floatNumber floatValue]);
        //NSInteger实际上是基本数据类型的typedef，64位的long或者32位的int
        myNumber = [NSNumber numberWithInteger:100];
        myInteger = [myNumber integerValue];
        NSLog(@&quot;NSInteger的值为: %li&quot;, (long)myInteger);
        // 数值比较
        NSComparisonResult ncr = [myNumber compare:floatNumber];
        NSLog(@&quot;比较结果为: %i&quot;, (int)ncr);
       
       
    }
    return 0;
}

</pre>
<p>
	关于NSLog函数，跟Java里一样，用%@可以替换字符串和对象的值，实际是调用了对象的description方法，就跟Java里的toString一个道理，没覆写的返回类名+地址。</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
#import &lt;Foundation/Foundation.h&gt;

@interface MyClass : NSObject

- (NSString *) description;

@end

@implementation MyClass

- (NSString *) description {
    return @&quot;MyClass&quot;;
}

@end

</pre>
<p>
	调用：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
  MyClass *mc = [MyClass new];
  NSLog(@&quot;打印一个类:%@&quot;, mc);
</pre>
<p>
	输出结果：</p>
<blockquote>
	<p>
		2014-06-28 13:31:47.518 ChapterFifteen[36796:303] 打印一个类:MyClass</p>
</blockquote>
<p>
	这里也可以看到构造NSString对象的方法。</p>
<p>
	先撸到这，继续看书。<br />
	&nbsp;</p>

