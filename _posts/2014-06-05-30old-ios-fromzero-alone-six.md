---
layout: post
title: 30岁码农从零单排iOS 第一季 6. 继承
date: 2014-06-05 12:20
author: onecoder
comments: true
categories: [iOS, objective-c基础, objective-c教程, swift]
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
	<a href="http://www.coderli.com"> OneCoder</a>单排着却突然杀出了个Swift，不过虽然被gank了，但是节奏不能乱，OC之路还要继续，相信对于那些已经精通iOS开发的人来说，换个Swift开发也只是轻轻松松的事情，因为他们掌握的是开发的精髓，而不仅仅是一个语法。所以，OneCoder决定这本攻略还是一定要撸完的～～</p>
<p>
	废话说多了，其实第八章OneCoder已经早早就读过了，只是一直没有时间去练习，最近又穿插翻译Swift的文档～～（这个权当调剂，也会继续～），再加上昨天意外病倒～所以拖到了现在。</p>
<p>
	对于多年Java屌来说，理解继承还是相当easy的。同样，Objective-C中的所有类也都默认继承自NSObject类，跟Java中继承自Object一样。继承的主要作用也是扩展和复写方法。</p>
<p>
	语法上，OC中的继承关系是声明在接口上，之前我们已经见过了：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
@interface ClassB: ClassA
</pre>
<p>
	Java中定义了四种访问权限，复杂的头疼，在OC里只有两种，攻略上说：<br />
	&nbsp;&nbsp;&nbsp;&nbsp; 要在子类中直接使用实例变量，必须现在接口部分声明，是实现部分声明和合成(synthesize)的实例变量是私有的，子类不能直接访问，需要明确定义或合成取值方法才能访问实例变量的值。</p>
<p>
	意思应该很明白了，不过容易误解的就是，这里提到的是对实例变量的访问权限，而不是方法，通过synthesize合成出来的set/get方法是可以访问的，同时也可以看到OC中，接口里也可以声明变量。机智啊。先撸一段，不然感觉好空虚：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
//
//  Rectangle.h
//  矩形接口
//  ChapterEight
//
//  Created by OneCoder on 14-6-4.
//  Copyright (c) 2014年 OneCoder. All rights reserved.
//

#import &lt;Foundation/Foundation.h&gt;
@class XYPoint;
@interface Rectangle : NSObject

@property int width, height;

// 获取坐标
- (XYPoint *) point;
// 设置坐标
- (void) setXYPoint: (XYPoint *) point;
// 求面积
- (int) area;
// 求周长
- (int) perimeter;
// 设置长和宽
- (void) setWidth: (int) w andHeight: (int) h;


@end

//
//  Rectangle.m
//  ChapterEight
//
//  Created by OneCoder on 14-6-4.
//  Copyright (c) 2014年 OneCoder. All rights reserved.
//

#import &quot;Rectangle.h&quot;

@implementation Rectangle

{
    XYPoint  *origin;
}

@synthesize width, height;

- (void) setXYPoint:(XYPoint *)point {
    origin = point;
}

- (XYPoint *) point {
    return origin;
}

- (int) area {
    return width * height;
}

- (int) perimeter {
    return (width + height) * 2;
}

- (void) setWidth:(int) w andHeight:(int) h {
    self.width = w;
    self.height = h;
}
@end

</pre>
<p>
	来个正方形接口继承矩形</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
//
//  Square.h
//  正方形，继承自矩形，是一种特殊的矩形。
//  ChapterEight
//
//  Created by OneCoder on 14-6-4.
//  Copyright (c) 2014年 OneCoder. All rights reserved.
//

#import &quot;Rectangle.h&quot;

@interface Square : Rectangle

// 设置正方形边长
- (void) setSide : (int) s;

@end

//
//  Square.m
//  ChapterEight
//
//  Created by OneCoder on 14-6-4.
//  Copyright (c) 2014年 OneCoder. All rights reserved.
//

#import &quot;Square.h&quot;

@implementation Square

- (void) setSide : (int) s {
    [self setWidth: s andHeight: s];
}

@end

</pre>
<p>
	测试类：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
//
//  main.m
//  ChapterEight
//
//  Created by OneCoder(Blog: http://www.coderli.com ) on 14-6-4.
//  Copyright (c) 2014年 OneCoder. All rights reserved.
//

#import &lt;Foundation/Foundation.h&gt;
#import &quot;Rectangle.h&quot;
#import &quot;Square.h&quot;

int main(int argc, const char * argv[])
{

    @autoreleasepool {
      
        Rectangle *rec = [Rectangle new];
        Square *squ = [Square new];
     
        [rec setWidth: 3 andHeight: 4];
        NSLog(@&quot;矩形的周长为: %i&quot;, [rec perimeter]);
        NSLog(@&quot;矩形的面积为: %i&quot;, [rec area]);
        [squ setSide:5];
        NSLog(@&quot;正方形的周长为: %i&quot;, [squ perimeter]);
        NSLog(@&quot;正方形的面积为: %i&quot;, [squ area]);
      
    }
    return 0;
}
</pre>
<p>
	可以看到，正方形继承自矩形，直接使用了矩形类里的求周长和面积的方法。同时扩展了自己的接口，简化了设置边长的方法。</p>
<p>
	在Rectangle接口开头，有一个@class的OC里用法。用@class指令可以提供效率，因为编译器不需要引入和处理整个XYPoint.h文件，只需要知道XYPoint是一个类名。当然，这里如果你用#import，自然也是可以的。</p>
<p>
	这个样例里，没有体现出方法的复写，不过和Java里都是一样的，所以也就没特别的练习了。</p>
<p>
	哦了，总体来说，有了Java屌的基础，撸起继承来相对比较简单，不过攻略里的下一章，感觉就有些新鲜的东西了～～OneCoder已经在看了，估计会分两次撸出来了～～</p>
<p>
	病了，休息了~~</p>

