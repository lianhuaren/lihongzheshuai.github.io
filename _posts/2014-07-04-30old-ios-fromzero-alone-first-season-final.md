---
layout: post
title: 30岁码农从零单排iOS 第一季 13. 完结篇
date: 2014-07-04 00:39
author: onecoder
comments: true
categories: [iOS, objective-c]
---
<blockquote>
	<p>
		第一季单排什么？是OneCoder学习《Objective-C程序设计》(第4版)的经过和总结。</p>
</blockquote>
<p>
	突然完结了？看到标题你可能以为写错了，怎么突然完结了。其实完结的只是第一季而已，因为《Objective-C程序设计》这本书除了最后一章的iOS界面开发，我都撸完了，UI开发部分，准备在第二季单排中集中学习。从零单排第二季的内容已经选定了就是《精通iOS开发》这本秘籍了，主要学的就是UI的开发。</p>
<p>
	不过第二季估计要在本月底才能开始了，因为明天OneCoder就要回家去迎接新生命的到来了：）</p>
<p>
	先不多说废话，先把第一季完美收官一下。上次才撸到15章，这次一下跨到20章，看似步子有点大，恐怕扯到蛋，其实不然。因为，后面大多是介绍Foundation框架中的常用类，也就是Java中的List，Map，File、深浅拷贝、序列化等。由于在Java中，多有涉及，所以概念不难理解，剩下就是一个对API熟能生巧的过程。所以，把这些章的内容，精选一下，撸一段代码如下：</p>
<pre class="brush:csharp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
//
//  main.m
//  ChapterFinal
//
//  Created by OneCoder(Blog: http://www.coderli.com ) on 14-7-3.
//  Copyright (c) 2014年 OneCoder. All rights reserved.
//

#import &lt;Foundation/Foundation.h&gt;


int main(int argc, const char * argv[])
{
   
    @autoreleasepool {
        //可变字符串，即初始化后还可修改。
        NSMutableString *mString = [NSMutableString stringWithString: @&quot;One&quot;];
        [mString appendString: @&quot;Coder&quot;];
        NSLog(@&quot;%@&quot;, mString);
        // 不可变数组,以nil代表结尾。
        NSArray *number = [NSArray arrayWithObjects:@&quot;one&quot;, @&quot;two&quot;, @&quot;three&quot;, nil];
        NSLog(@&quot;%@&quot;, number);
        // 可变数组
        NSMutableArray *numbers = [NSMutableArray arrayWithArray:number];
        [numbers addObject:@&quot;four&quot;];
        NSLog(@&quot;%@&quot;, numbers);
        //字典，即Java中的Map
        NSMutableDictionary *dictionary = [NSMutableDictionary dictionary];
        [dictionary setValue:@&quot;OneCoder&quot; forKey: @&quot;name&quot;];
        [dictionary setValue:@&quot;http://www.coderli.com&quot; forKey: @&quot;blog&quot;];
        NSLog(@&quot;Name: %@&quot;, [dictionary valueForKey:@&quot;name&quot;]);
        NSLog(@&quot;Blog: %@&quot;, [dictionary valueForKey:@&quot;blog&quot;]);
        // 文件操作
        NSFileManager *fManager = [NSFileManager defaultManager];
        // 当前路径
        NSLog(@&quot;当前路径: %@&quot;, [fManager currentDirectoryPath]);
        NSString *fName = @&quot;/Users/apple/Documents/Develop/iOS/fromzero-ios/ChapterFinal/ChapterFinal/testFile&quot;;
        if ([fManager fileExistsAtPath:fName] == NO) {
            NSLog(@&quot;文件不存在&quot;);
        } else {
            NSLog(@&quot;文件存在&quot;);
        }
        //读取文件内容到内存
        [fManager contentsAtPath:fName];
        // 输出文件内容
        NSLog(@&quot;%@&quot;, [NSString stringWithContentsOfFile:fName encoding:NSUTF8StringEncoding error:NULL]);
        // 访问网络
        NSURL *onecoder = [NSURL URLWithString: @&quot;http://www.baidu.com&quot;];
        NSString *webContent = [NSString stringWithContentsOfURL:onecoder encoding:NSUTF8StringEncoding error:NULL];
        NSLog(@&quot;博客内容: %@&quot;, webContent);
        // 对象序列化归档到文件
        NSString *archiveFileName = @&quot;/Users/apple/Documents/Develop/iOS/fromzero-ios/ChapterFinal/ChapterFinal/archivefile.archive&quot;;
        [NSKeyedArchiver archiveRootObject:dictionary toFile:archiveFileName];
        // 从归档文件反序列化对象
        NSMutableDictionary *newDic = [NSKeyedUnarchiver unarchiveObjectWithFile:archiveFileName];
        NSLog(@&quot;Name: %@&quot;, [newDic valueForKey:@&quot;name&quot;]);
        NSLog(@&quot;Blog: %@&quot;, [newDic valueForKey:@&quot;blog&quot;]);
// NSProcessInfo练习
        NSProcessInfo *pInfo = [NSProcessInfo processInfo];
        NSArray *args = [pInfo arguments];
        NSLog(@&quot;参数: %@, 名字: %@&quot;, args,[pInfo processName]);

    }
    return 0;
}

</pre>
<p>
	输出：</p>
<blockquote>
	<p>
		<br />
		2014-07-04 00:21:50.653 ChapterFinal[45271:303] OneCoder<br />
		2014-07-04 00:21:50.654 ChapterFinal[45271:303] (<br />
		&nbsp;&nbsp;&nbsp; one,<br />
		&nbsp;&nbsp;&nbsp; two,<br />
		&nbsp;&nbsp;&nbsp; three<br />
		)<br />
		2014-07-04 00:21:50.655 ChapterFinal[45271:303] (<br />
		&nbsp;&nbsp;&nbsp; one,<br />
		&nbsp;&nbsp;&nbsp; two,<br />
		&nbsp;&nbsp;&nbsp; three,<br />
		&nbsp;&nbsp;&nbsp; four<br />
		)<br />
		2014-07-04 00:21:50.655 ChapterFinal[45271:303] Name: OneCoder<br />
		2014-07-04 00:21:50.655 ChapterFinal[45271:303] Blog: http://www.coderli.com<br />
		2014-07-04 00:21:50.655 ChapterFinal[45271:303] 当前路径: /Users/apple/Library/Developer/Xcode/DerivedData/ChapterFinal-alqcozvqaqqeixhlhgtmpbtidnaf/Build/Products/Debug<br />
		2014-07-04 00:21:50.656 ChapterFinal[45271:303] 文件存在<br />
		2014-07-04 00:21:50.656 ChapterFinal[45271:303] Just an empty file<br />
		2014-07-04 00:21:50.730 ChapterFinal[45271:303] Name: OneCoder<br />
		2014-07-04 00:21:50.730 ChapterFinal[45271:303] Blog: http://www.coderli.com<br />
		2014-07-04 00:29:33.005 ChapterFinal[45354:303] 参数: (<br />
		&nbsp;&nbsp;&nbsp; &quot;/Users/apple/Library/Developer/Xcode/DerivedData/ChapterFinal-alqcozvqaqqeixhlhgtmpbtidnaf/Build/Products/Debug/ChapterFinal&quot;<br />
		), 名字: ChapterFinal<br />
		Program ended with exit code: 0</p>
</blockquote>
<p>
	<br />
	这里，省略访问网络部分的输出，因为太长。上面的代码只是一些基本的核心功能的联系用于熟悉一下api操作，更多的接口需要在实战中慢慢摸索。</p>
<p>
	第一季的结尾，感觉有些草草，赶在回家前收尾，也要断个念想。后几章还有一些点没有包含在内。不过OneCoder大概是理解的。</p>
<p>
	夜深了，很多话，暂且放下吧。很高兴自己坚持读完了一本书，最近也喜欢上了读书。准备从零单排iOS的第二季的同时，OneCoder也考虑模仿一下所谓的高分局系列，通过《编程之美》等实例书籍，逐步接触一下算法知识。希望能给自己找到一条算法的学习之路。</p>
<p>
	睡了，安。</p>

