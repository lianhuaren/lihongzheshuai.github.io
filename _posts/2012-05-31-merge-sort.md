---
layout: post
title: 合并排序 - Java实现
date: 2012-05-31 22:35
author: onecoder
comments: true
categories: [Java, Java 排序, 算法导论]
---
<leftlength; else="" for="" i="=" if="" int="" j="0;" k="0;" left="mergeSort(left);" leftlength="" right="mergeSort(right);">
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
package lhz.algorithm.chapter.six;
/**
 * &ldquo;优先级队列&rdquo;，《算法导论》6.5章节
 * 原文摘要：
 * A priority queue is a data structure for maintaining a set S of elements, each with an
 * associated value called a key. A max-priority queue supports the following operations.
 * &bull; INSERT(S, x) inserts the element x into the set S. This operation could be written as S&larr; S{x}.
 * &bull; MAXIMUM(S) returns the element of S with the largest key.
 * &bull; EXTRACT-MAX(S) removes and returns the element of S with the largest key.
 * &bull; INCREASE-KEY(S, x, k) increases the value of element x&#39;s key to the new value k,
 * which is assumed to be at least as large as x&#39;s current key value.
 * 堆的一种实际应用，可用于任务调度队列等。包含四个操作方法。
 * @author lihzh(OneCoder)
 * @blog http://www.coderli.com
 */
public class PriorityQueue {
	
	private static final int DEFAULT_CAPACITY_VALUE = 16;
	//初始化一个长度为16的队列（可作为构造参数)，此处选择16参考HashMap的初始化值
	private int[] queue;
	//堆大小
	private int heapSize = 0;

	public static void main(String[] args) {
		PriorityQueue q = new PriorityQueue();
		q.insert(2);
		q.insert(6);
		q.insert(3);
		q.insert(8);
		q.insert(7);
		q.insert(9);
		q.insert(1);
		q.insert(10);
		q.insert(9);
		System.out.println(q.extractMax());
		System.out.println(q.extractMax());
		q.insert(9);
		q.insert(1);
		q.insert(10);
		System.out.println(q.extractMax());
		System.out.println(q.extractMax());
		System.out.println(q.extractMax());
		System.out.println(q.extractMax());
		System.out.println(q.extractMax());
		System.out.println(q.extractMax());
		System.out.println(q.extractMax());
	}
	
	public PriorityQueue(int capacity) {
		this.queue = new int[capacity];
	}
	
	public PriorityQueue() {
		this(DEFAULT_CAPACITY_VALUE);
	}
	
	/**
	 * 返回当前最大值
	 * @return
	 */
	public int maximum() {
		return queue[0];
	}
	
	/**
	 * 往优先级队列出，插入一个元素
	 * 利用INCREASE-Key方法，从堆的最后增加元素
	 * 伪代码：
	 * MAX-HEAP-INSERT(A, key)
	 * 1 heap-size[A] &larr; heap-size[A] + 1
	 * 2 A[heap-size[A]] &larr; -&infin;
	 * 3 HEAP-INCREASE-KEY(A, heap-size[A], key)
	 * 时间复杂度：O(lg n)
	 * @param value 待插入元素
	 */
	public void insert(int value) {
		//注意堆容量和数组索引的错位 1
		queue[heapSize] = value;
		heapSize++;
		increaceKey(heapSize, value);
	}
	
	/**
	 * 增加给定索引位元素的值，并重新构成MaxHeap。
	 * 新值必须大于等于原有值
	 * 伪代码：
	 * HEAP-INCREASE-KEY(A, i, key)
	 * 1 if key &lt; A[i]
	 * 2 then error &quot;new key is smaller than current key&quot;
	 * 3 A[i] &larr; key
	 * 4 while i &gt; 1 and A[PARENT(i)] &lt; A[i]
	 * 5 do exchange A[i] &harr; A[PARENT(i)]
	 * 6 i &larr; PARENT(i)
	 * 时间复杂度：O(lg n)
	 * @param index 索引位
	 * @param newValue 新值
	 */
	public void increaceKey (int heapIndex, int newValue) {
		if (newValue &lt; queue[heapIndex-1]) {
			System.err.println(&quot;错误：新值小于原有值！&quot;);
			return;
		}
		queue[heapIndex-1] = newValue;
		int parentIndex = heapIndex / 2;
		while (parentIndex &gt; 0 &amp;&amp; queue[parentIndex-1] &lt; newValue ) {
			int temp = queue[parentIndex-1];
			queue[parentIndex-1] = newValue;
			queue[heapIndex-1] = temp;
			heapIndex = parentIndex;
			parentIndex = parentIndex / 2;
		}
	}
	
	/**
	 * 返回堆顶元素（最大值），并且将堆顶元素移除
	 * 伪代码：
	 * HEAP-EXTRACT-MAX(A)
	 * 1 if heap-size[A] &lt; 1
	 * 2 then error &quot;heap underflow&quot;
	 * 3 max &larr; A[1]
	 * 4 A[1] &larr; A[heap-size[A]]
	 * 5 heap-size[A] &larr; heap-size[A] - 1
	 * 6 MAX-HEAPIFY(A, 1)
	 * 7 return max
	 * 时间复杂度：O(lg n),
	 * @return
	 */
	public int extractMax() {
		if (heapSize &lt; 1) {
			System.err.println(&quot;堆中已经没有元素!&quot;);
			return -1;
		}
		int max = queue[0];
		queue[0] = queue[heapSize-1];
		heapSize--;
		maxHeapify(queue, 1);
		return max;
	}
	
	
	/**
	 * 之前介绍的保持最大堆的算法
	 * @param array
	 * @param index
	 */
	private  void maxHeapify(int[] array, int index) {
		int l = index * 2;
		int r = l + 1;
		int largest;
		//如果左叶子节点索引小于堆大小，比较当前值和左叶子节点的值，取值大的索引值
		if (l &lt;= heapSize &amp;&amp; array[l-1] &gt; array[index-1]) {
			largest = l;
		} else {
			largest = index;
		}
		//如果右叶子节点索引小于堆大小，比较右叶子节点和之前比较得出的较大值，取大的索引值
		if (r &lt;= heapSize &amp;&amp; array[r-1] &gt; array[largest-1]) {
			largest = r;
		}
		//交换位置，并继续递归调用该方法调整位置。
		if (largest != index) {
			int temp = array[index-1];
			array[index-1] = array[largest-1];
			array[largest-1] = temp;
			maxHeapify(array, largest);
		}
	}
	
	public int[] getQueue() {
		return queue;
	}
}

</pre>
</leftlength;>
