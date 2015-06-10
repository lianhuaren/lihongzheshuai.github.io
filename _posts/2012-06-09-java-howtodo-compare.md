---
layout: post
title: 代码最佳实践-如何做compare
date: 2012-06-09 23:44
author: onecoder
comments: true
categories: [compare, Java, Java进阶, 技巧]
---
<p>
	最近在公司内部做了一些收集和整理的工作，关于trouble shooting和performace tuning 中遇到并解决的典型问题，做了一些内部分享。我整理了一下，准备陆续放上来分享给大家。 这些问题，单个看每个问题都不算复杂或高深，但是都是在实际项目开发中出现并一度造成困扰的，而且带有一定的普适性，具体表现为不知道这些问题的同学很容易在日常开发中中招。因此我们开了一个专题，叫做代码最佳实践，似乎名字起的有点大......</p>
先来看看第一个，如何做compare。 先看案例,问题的表现很简单，就是在排序后的结果中有时会很惊讶的发现排序错误。我们不纠结于具体的错误表现细节和排查的过程，直接来看最终被检查出问题所在的代码，这是一个很普通的Comparator接口实现：<br />
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
private static class keyOrderComparator implements Comparator {
    public int compare(Persistent p1, Persistent p2) {
        return (int) (p1.getId().getKey() - p2.getId().getKey());
    }
}&nbsp;</pre>
<p>
	代码中的比较逻辑很简单，比较Persistent对象的id的key值就OK，实现中将两个key简单做一次减法运算，将结果作为compare()方法的返回值。如果p1的key大于 p2的key，则&rdquo;p1.getId().getKey() &ndash; p2.getId().getKey()&rdquo;的结果大于0，而compareTo()方法返回一个大于0的整数表示比较结果为&rdquo;参数p1大于参数p2&Prime;。</p>
<p>
	但麻烦出现在key的数据类型上，这是一个long类型，因此减法运算的结果也是一个long，为了满足compare()方法要求返回int的要求，在return前做了一次强制类型转换。而问题就出现在这里：从long到int的强制类型转换是有风险的，如果long的数字超过了int所能表示的范围[Integer.Min_VALUE, Integer.Max_VALUE]，则会发生&rdquo;数据溢出&rdquo;(data overflow)。</p>
我们可以试着执行以下代码 System.out.println((int) (30000000000L &ndash; 1)); , 会发现它的结果是一个&rdquo;-64771073&Prime;，和意想中的29999999999完全不同，重要的是符号变了：从一个正数变成了负数！这直接导致了compare()方法得出了一个令人惊讶的比较结果：30000000000 比 1 小！<br />
解决方式也很简单，不要做强制类型转换：<br />
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
private static class keyOrderComparator implements Comparator {
    public int compare(Persistent p1, Persistent p2) {
        long key1 = p1.getId().getKey();
        long key2 = p2.getId().getKey();
	if (key1 == key2) {
	    return 0;
	} else {
	    return key1 &gt; key2 ? 1 : -1;
	}
    }
}
</pre>
在这个简单案例当中，有一个比较明显的地方可以帮助我们发现问题，就是(int)这个强制类型转换，稍有经验的同学就会第一时间反应过来：long到int是有数据溢出风险的。那如果我们将这个案例稍微修改一下，假设p1.getId().getKey()返回的就是普通的int，结果会如何：<br />
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
private static class keyOrderComparator implements Comparator {
    public int compare(Persistent p1, Persistent p2) {
        return p1.getId().getKey() - p2.getId().getKey();
    }
}
</pre>
这段代码貌似就没有问题啦？呵呵，让我们把这段代码的业务含义去掉，退化为一个普通的int比较：&nbsp;private static class IntegerOrderComparator implements Comparator {
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
    public int compare(Integer p1, Integer p2) {
        return p1 - p2;
    }
}&nbsp;</pre>
这下应该能看出来了吧？如果p1=2147483647即Integer.MAX_VALUE，而p2=-1，则p1 &ndash; p2 = Integer.MAX_VALUE &ndash; (-1) = -2147483648 ! IntegerOrderComparator 会给出一个令人目瞪口呆的比较结果：2147483647 比 -1 小！类似的，在 p1= -2147483648 (Integer.MIN_VALUE), p2 = 1时，IntegerOrderComparator 同样会给出类似荒唐的比较结果：-2147483648 比 1 大！<br />
导致错误发生的原因依然是&rdquo;数据溢出&rdquo;！和前面long到int的强制类型转换不同，这次数据溢出发生在int与int之间做数学运算。
<p>
	我们来看问题发生在哪里：&rdquo;int &ndash; int&rdquo;这样的简单的运算，在我们的数学常识中，两个整型相减结果肯定还是整型，一个正数减一个负数结果肯定是正数，一个负数减一个正数结果肯定是负数&hellip;&hellip;但是这里的数学常识中所谓的&rdquo;整型&rdquo;，其取值范围可以是无穷小到无穷大，而java语言(其他语言也是类似)中的int，只能表示[Integer.Min_VALUE, Integer.Max_VALUE]，即[-2147483648, 2147483647]这样一个范围。一旦运算的结果超过这个范围，就会发生数据溢出。</p>
因此，在java中，类似&rdquo;int + int&rdquo;, &ldquo;int &ndash; int&rdquo;, &ldquo;int * int&rdquo; 这样的运算结果，用int来表示是不安全的，需要使用更大的数据类型比如long来。上面的代码可以修订为：<br />
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
private static class IntegerOrderComparator implements Comparator {
    public int compare(Integer p1, Integer p2) {
        long diff = p1 - p2;
	return diff == 0 ? 0 : (diff &gt; 0 : 1 : -1);
    }
}
</pre>
<p>
	但是这种compare的写法，遇到数据范围更大的数据类型时依然有麻烦，因为总是要找到一个比它数据范围还要大的数据类型来承载这个diff的结果。因此还是推荐使用前面的比较方法：不做减法，直接做等于和大于/小于的比较。 最后总结一下这个案例： 1. compare方法实现时，尽量不要用&quot;return p1 - p2&quot;这种写法 2. 但凡进行数值运算时，都要小心考虑数据溢出的风险 3. 做trouble shooting时，要留意可能的数据溢出</p>
<cite>转自：http://www.blogjava.net/aoxj/archive/2012/06/09/380425.html</cite>
