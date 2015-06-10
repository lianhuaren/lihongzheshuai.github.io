---
layout: post
title: 30岁码农从零单排iOS 第一季 7. 多态、动态类型和动态绑定
date: 2014-06-08 18:52
author: onecoder
comments: true
categories: [iOS, iOS基础, iOS教程, objective-c基础, objective-c教程]
---
<blockquote>
	<p>
		前言要精简了～</p>
	<p>
		第一季单排什么？是OneCoder学习《Objective-C程序设计》(第4版)的经过和总结。</p>
</blockquote>
<p>
	间隔的似乎有点久&hellip;&hellip;因为嘴里开刀啦。突出一个疼字啊～～本来来打算分两次撸完第九章的，不过单排记录一直没写，结果第九章攻略直接看完了～～那就一并撸了吧～～</p>
<p>
	第九章攻略，理论其实部分其实不多～～需要撸的代码可能相对多点。首先是一个概念：多态。</p>
<blockquote>
	<p>
		使不同的类共享相同方法名称的能力成为多态。它让你可以开发一组类，这组类的每一个类都能相应相同的方法名。</p>
</blockquote>
<p>
	意思够清楚了，不用废话了。。</p>
<p>
	今天介绍OC里的特殊类型，id，不知道为什么是这个名字。id是一种通用的对象类型，id可以用来存储任何类的对象。这种类型优势，可看下面的代码：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
//
//  main.m
//  ChapterNine
//
//  Created by OneCoder(Blog: http://www.coderli.com ) on 14-6-8.
//  Copyright (c) 2014年 OneCoder. All rights reserved.
//

#import &lt;Foundation/Foundation.h&gt;
#import &quot;ClassA.h&quot;
#import &quot;ClassB.h&quot;
int main(int argc, const char * argv[])
{

    @autoreleasepool {
        // 声明一个id类型的变量。
        id intance;
        intance = [ClassA new];
        [intance print];
        intance = [[ClassB alloc] init];
        [intance print];
       
    }
    return 0;
}
</pre>
<p>
	这里省略ClassA和ClassB的代码，只是各自实现print方法。运行输出如下：</p>
<blockquote>
	<p>
		2014-06-08 12:38:48.120 ChapterNine[7764:303] 这是ClassA<br />
		2014-06-08 12:38:48.122 ChapterNine[7764:303] 这是ClassB<br />
		Program ended with exit code: 0</p>
</blockquote>
<p>
	上述代码，一方面体现了多态的概念，另一方面可以看到id这个类型的特点，可以表示任何类类型的变量。代码在运行期会检测出实际的类型，分别调用不同类中的实现。</p>
<p>
	不过，由于存储在id变量中的对象类型在编译时无法确定，也就是说如果调用了无效的方法，只有在运行期才会抛出错误，编译器无法得知。这也是一个弊端。所以，我们在选择使用id还是静态类型的时候，需要按需考虑。</p>
<p>
	有了动态类型，了解到上面提到的问题，自然会想到，我在编码的时候怎么检查你的对象是哪个类的，是否支持某个方法。Java里有instanceof，Objective-C中自然有他的办法。攻略里有个表，很有用。</p>
<blockquote>
	<p>
		- (BOOL) isKindOfClass : class-object&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象是不是class-object或其子类的成员<br />
		- (BOOL) isMemberOfClass : class=object&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象是不是class-object的成员<br />
		- (BOOL) respondsToSelector : Selector&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象是否能够响应selector所指定的方法<br />
		+ (BOOL) instancesRespondToSelector : Selector&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 指定的类的实例是否能响应selector<br />
		+ (BOOL) isSubclassOfClass : class-object&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象是否是指定类的子类<br />
		- (id) performSelector : selector&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 应用selector指定的方法<br />
		- (id) performSelector : selector withObject : object&nbsp;&nbsp;&nbsp; 应用selector指定的方法，传递参数object<br />
		- (id) performSelector : selector withObject : object1&nbsp; 应用selector指定的方法，传递参数object1和&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<br />
		withObject object2&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object2</p>
</blockquote>
<p>
	表格里的参数Selector可以通过对一个方法名加上@selector来获得</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
@selector (alloc)
</pre>
<p>
	学会用这些方法最好的办法就是撸一把了。代码如下：</p>
<p>
	接口定义：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
//
//  ClassA.h
//  ChapterNine
//
//  Created by OneCoder on 14-6-8.
//  Copyright (c) 2014年 OneCoder. All rights reserved.
//

#import &lt;Foundation/Foundation.h&gt;

@interface ClassA : NSObject

- (void) print;
- (void) methodA : (int) a;

@end

//
//  SonOfClassA.h
//  ChapterNine
//
//  Created by OneCoder on 14-6-8.
//  Copyright (c) 2014年 OneCoder. All rights reserved.
//
// ClassA的子类
#import &quot;ClassA.h&quot;

@interface SonOfClassA : ClassA

- (void) sonMethodA : (int) a;
- (void) methodA : (int) a : (int) b;

@end
</pre>
<p>
	测试类</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
//
//  main.m
//  ChapterNine
//
//  Created by OneCoder(Blog: http://www.coderli.com ) on 14-6-8.
//  Copyright (c) 2014年 OneCoder. All rights reserved.
//

#import &lt;Foundation/Foundation.h&gt;
#import &quot;ClassA.h&quot;
#import &quot;ClassB.h&quot;
#import &quot;SonOfClassA.h&quot;
int main(int argc, const char * argv[])
{
   
    @autoreleasepool {
        // 声明一个id类型的变量。
        id intance;
        intance = [ClassA new];
        [intance print];
        intance = [[ClassB alloc] init];
        [intance print];
        // 判断并输出是否满足条件1满足，0不满足
        ClassA *fatherA = [ClassA new];
        SonOfClassA *sonA = [SonOfClassA new];
        NSLog(@&quot;判断sonA是不是ClassA或其子类的成员 %i&quot;, [sonA isKindOfClass : [ClassA class]]);//1
        NSLog(@&quot;判断sonA是不是ClassA类的成员 %i&quot;, [sonA isMemberOfClass : [ClassA class]]);//0
        NSLog(@&quot;判断sonA是不是SonOfClassA类的成员 %i&quot;, [sonA isMemberOfClass : [SonOfClassA class]]);//0
        NSLog(@&quot;判断fatherA是否响应方法methodA : (int) a %i&quot;, [fatherA respondsToSelector : @selector (methodA:)]);//1
        NSLog(@&quot;判断fatherA是否响应方法methodA:(int) a:(int) b %i&quot;, [fatherA respondsToSelector : @selector (methodA: :)]);//0
        NSLog(@&quot;判断sonA是否响应方法methodA:(int) a:(int) b %i&quot;, [sonA respondsToSelector : @selector (methodA: :)]);//1
        NSLog(@&quot;判断ClassA类的实例是否响应方法methodA:(int) a %i&quot;, [ClassA instancesRespondToSelector : @selector (methodA: )]);//1
        NSLog(@&quot;判断类SonOfClassA是否是ClassA类的子类 %i&quot;, [SonOfClassA isSubclassOfClass : [ClassA class]]);//1
        [fatherA performSelector: @selector(print)];
    }
    return 0;
}
</pre>
<p>
	输出如下：</p>
<blockquote>
	<p>
		2014-06-08 17:03:57.023 ChapterNine[9442:303] 这是ClassA<br />
		2014-06-08 17:03:57.024 ChapterNine[9442:303] 这是ClassB<br />
		2014-06-08 17:03:57.025 ChapterNine[9442:303] 判断sonA是不是ClassA或其子类的成员 1<br />
		2014-06-08 17:03:57.025 ChapterNine[9442:303] 判断sonA是不是ClassA类的成员 0<br />
		2014-06-08 17:03:57.025 ChapterNine[9442:303] 判断sonA是不是SonOfClassA类的成员 1<br />
		2014-06-08 17:03:57.026 ChapterNine[9442:303] 判断fatherA是否响应方法methodA : (int) a 1<br />
		2014-06-08 17:03:57.026 ChapterNine[9442:303] 判断fatherA是否响应方法methodA:(int) a:(int) b 0<br />
		2014-06-08 17:03:57.026 ChapterNine[9442:303] 判断sonA是否响应方法methodA:(int) a:(int) b 1<br />
		2014-06-08 17:03:57.026 ChapterNine[9442:303] 判断ClassA类的实例是否响应方法methodA:(int) a 1<br />
		2014-06-08 17:03:57.027 ChapterNine[9442:303] 判断类SonOfClassA是否是ClassA类的子类 1<br />
		2014-06-08 17:03:57.027 ChapterNine[9442:303] 这是ClassA<br />
		Program ended with exit code: 0</p>
</blockquote>
<p>
	我的测试代码，和攻略里的略有不同，主要是测试几个自己不太明白的点，总结了一下:</p>
<blockquote>
	<p>
		1、原来+的方法是类似Java里的static方法，用类名直接调用，我以前理解错了好像。<br />
		2、@selector获取方法时，攻略里强调要写冒号，这里OneCoder特意测试了一下Java的重载的写法，其实冒号(:) 的个数就是参数的个数而已。如果是采用加上参数名的写法，那这里也要带上。例如: @selector(setWidth : andHeight:) 这就可以理解确定哪个方法了。</p>
</blockquote>
<p>
	看了测试代码，估计应该对上面的表格的理解很清晰了。本章攻略还省最后一部分，就是关于异常捕获try catch的。概念上，相信对于Java屌来说再熟悉不过了。就是捕获可能出现的问题。这里主要学习的是OC里的写法。代码样例：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
@try {
            NSException *e = [NSException exceptionWithName:@&quot;MyName&quot; reason:@&quot;MyReason&quot; userInfo: [NSDictionary new]];
            @throw e;
           
        }
        @catch (NSException *exception) {
            NSLog(@&quot;捕获到异常: %@%@&quot;, [exception name], [exception reason]);
        }
        @finally {
            NSLog(@&quot;Finally里的代码&quot;);
        }&nbsp;</pre>
<p>
	这里OneCoder是自己编写的样例代码，攻略里指提到了@throw和@finally，并没有给出样例。而且，我感觉对finally的翻译真是无法理解啊。</p>
<p>
	在构造NSException异常的时候，OneCoder还用到了刚刚才理解的知识，开始想用[NSException new]获取实例，结果失败，机智的查看源码发现有个+的函数，于是就有了上面的代码。</p>
<p>
	到此，第九章就算撸完了。不要走开～第十章很快开撸～～发现有了Java护体，撸的飞起啊。</p>

