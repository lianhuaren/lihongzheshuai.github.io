---
layout: post
title: 数组全排列递归算法 C语言实现
date: 2013-02-28 22:26
author: onecoder
comments: true
categories: [C, 全排列, 数据结构, 数据结构, 算法]
---
<p>
	《数据结构》中的一个小例子算法，对于<a href="http://www.coderli.com">OneCoder</a>这种初学者还是值得思考一下的。遂进行了动手实现。实现思路记录在注释中。</p>
<pre class="brush:cpp;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">

//递归全排列算法, 给定一个数组，打印出其全排列的算法
#include&lt;stdio.h&gt;
//利用宏，定义字符交换
#define SWAP(x, y, t)((t) = (x), (x) = (y), (y) = (t));
void perm(char *list, int i, int n);
int main() {
    //这个不能使用指针，会报Bus error:10 error。修改字符串的值是不合法。
    char array[] = &quot;abcd&quot;;
    perm(array, 0, 3);   
}
/*
递归思路，定义perm是一个能对数组进行全排列的函数。
也就是当需要对数组进行排列的时候，只要按照参数规则调用perm即可。按照这种思路
和假设的前提实现perm，即给定一个数组{a,b,c,d}，全排列的方式即在首位是
a的情况下，全排列剩下的，然后交换1-2位，即在首位是b的情况下，全排列剩下的，依次类推。落到代码即，
依次交换首位，然后用perm函数自身对剩下的数组进行全排列。perm函数内，只要判断出来
数组中只有一个元素的时候，无需再排列，直接打印出数组即可。
因为依次对数组的1-2,1-3,1-4...位进行交换，所以每次交换排列后，需要再次交换使数组
归位。
*/
void perm(char *list, int i, int n) {
    int j = 0;
    char temp;
    if(i == n) {
        printf(&quot;%s\n&quot;,list);
    } else {
        for (j = i; j &lt;= n; j++) {
            SWAP(list[i],list[j],temp);
            perm(list,i+1,n);
            SWAP(list[i],list[j],temp);
        }
    }   
}
</pre>
<p>
	&nbsp;</p>

