---
layout: post
title: C++ 语法特性学习小记
date: 2013-01-26 22:53
author: onecoder
comments: true
categories: [c++ primer, C\C++, 类, 语法]
---
<p>
	快速的浏览了《C++ Primer》第二章的一些内容，主要寻找与Java不同的点。编写一些小程序加深印象。笔记都记录在代码的注释中。 第二章中还有关于类的定义的介绍，学习代码如下，在编译和纠错的过程中，还是体会到了很多C++和Java不同的地方。</p>
<pre class="brush:cpp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
//OneCoder练习之用。Blog:http://www.coderli.com
#include&lt;iostream&gt;
#include&quot;ChapterTwo-B.h&quot;
// const 约相当于Java中的&lsquo;final&rsquo;关键字,
// extern声明在函数外时，才可以在声明的时候含有初始化式。
extern const int declareInA = 256;
//main函数的返回值是int，而不是void。
int main(){
using namespace std;
  extern const int b;
  cout &lt;&lt; &quot;External Int B value is: &quot; &lt;&lt; b &lt;&lt; endl;
  // 定义引用，理解引用，不论修改引用值还是原始值，都会同步变化。
  int i = 1234;
  int &amp;r = i;
  r = 3456;
  cout &lt;&lt; &quot;After set r = 3456, i= &quot; &lt;&lt; i &lt;&lt; &quot; and r = &quot; &lt;&lt; r &lt;&lt;endl;
  i = 4567;
  cout &lt;&lt; &quot;After set i = 4567, i= &quot; &lt;&lt; i &lt;&lt; &quot; and r = &quot; &lt;&lt; r &lt;&lt;endl;
  // 考察可否定义引用的引用
  int &amp;r2 = r;
  cout &lt;&lt; &quot;r2= &quot; &lt;&lt; r2 &lt;&lt; endl;
  // 学习typedef关键字，定义某类型的同义词
  typedef double mydouble;
  mydouble d1 = 0.24f;
  double d2 = 0.365f;
  cout &lt;&lt; &quot;d1 + d2= &quot;&lt;&lt;d1 + d2 &lt;&lt;endl;
  // 学习枚举类型, C++中枚举的值是整数，默认自动递增1。
  // 打印时，实际输出的是整型值，而Java中输出的是字符串。
  enum ProgramLanguage{Java, C, Python=6};
  ProgramLanguage lanOne = Java;
  ProgramLanguage lanTwo = Python;
  cout &lt;&lt; &quot;Language One = &quot; &lt;&lt; Java &lt;&lt; endl;
  cout &lt;&lt; &quot;Language Two = &quot; &lt;&lt; Python &lt;&lt; endl;
  cout &lt;&lt; &quot;Language C value = &quot; &lt;&lt; C &lt;&lt; endl;
  // 测试引用一个类, 默认调用无参构造函数
  FirstClass::FirstClass firstClass;
  cout &lt;&lt; &quot;Class name by method: &quot; &lt;&lt; firstClass.getName() &lt;&lt; endl;
  return 0;
}
</pre>
<p>
	ChapterTwo-B.h定义：</p>
<pre class="brush:cpp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
struct FirstClass {
std::string getName();
};
</pre>
<p>
	ChapterTwoClass.cpp</p>
<pre class="brush:cpp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
#include&lt;iostream&gt;
#include&quot;ChapterTwo-B.h&quot;

  extern int b = 1234567;
  std::string FirstClass::getName(){
    return &quot;FirstClass&quot;;     
}
</pre>
<p>
	类的定义和Java最直观的区别就是类名和文件不用一致，这里<a href="http://www.coderli.com">OneCoder</a>也是特意如此命名，顺便做个测试。类的使用方式也不太一样，这里还真耽误了些时间。不说了，再继续学习吧。&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;</p>

