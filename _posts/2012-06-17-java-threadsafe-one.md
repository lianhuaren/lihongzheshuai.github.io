---
layout: post
title: Java线程安全总结
date: 2012-06-17 15:52
author: onecoder
comments: true
categories: [Java, 多线程, 多线程, 线程安全]
---
<p style="padding:0px;font-family:helvetica, tahoma, arial, sans-serif;font-size:14px;line-height:25.200000762939453px;text-align:left;margin-top:0px;margin-bottom:0px;">
	<span style="font-size:small"><span style="font-size:medium"><strong>浅谈java内存模型</strong> </span><br />
	&nbsp; 不同的平台，内存模型是不一样的，但是jvm的内存模型规范是统一的。其实java的多线程并发问题最终都会反映在java的内存模型上，所谓线程安全无 非是要控制多个线程对某个资源的有序访问或修改。总结java的内存模型，要解决两个主要的问题：可见性和有序性。我们都知道计算机有高速缓存的存在，处 理器并不是每次处理数据都是取内存的。JVM定义了自己的内存模型，屏蔽了底层平台内存管理细节，对于java开发人员，要清楚在jvm内存模型的基础 上，如果解决多线程的可见性和有序性。<br />
	&nbsp; <span style="color:#ff0000">那么，何谓可见性？</span> 多个线程之间是不能互相传递数据通信的，它们之间的沟通只能通过共享变量来进行。Java内存模型（JMM）规定了jvm有主内存，主内存是多个线程共享 的。当new一个对象的时候，也是被分配在主内存中，每个线程都有自己的工作内存，工作内存存储了主存的某些对象的副本，当然线程的工作内存大小是有限制 的。当线程操作某个对象时，执行顺序如下：<br />
	<span style="color:#3366ff"> (1) 从主存复制变量到当前工作内存 (read and load)<br />
	(2) 执行代码，改变共享变量值 (use and assign)<br />
	(3) 用工作内存数据刷新主存相关内容 (store and write)</span></span></p>
<p style="padding:0px;font-family:helvetica, tahoma, arial, sans-serif;font-size:14px;line-height:25.200000762939453px;text-align:left;margin-top:0px;margin-bottom:0px;">
	<span style="font-size:small">JVM规范定义了线程对主存的操作指 令：read，load，use，assign，store，write。当一个共享变量在多个线程的工作内存中都有副本时，如果一个线程修改了这个共享 变量，那么其他线程应该能够看到这个被修改后的值，这就是多线程的可见性问题。<br />
	<span style="color:#ff0000">那么，什么是有序性呢</span> ？线程在引用变量时不能直接从主内存中引用,如果线程工作内存中没有该变量,则会从主内存中拷贝一个副本到工作内存中,这个过程为read-load,完 成后线程会引用该副本。当同一线程再度引用该字段时,有可能重新从主存中获取变量副本(read-load-use),也有可能直接引用原来的副本 (use),也就是说 read,load,use顺序可以由JVM实现系统决定。<br />
	线程不能直接为主存中中字段赋值，它会将值指定给工作内存中的变量副本(assign),完成后这个变量副本会同步到主存储区(store- write)，至于何时同步过去，根据JVM实现系统决定.有该字段,则会从主内存中将该字段赋值到工作内存中,这个过程为read-load,完成后线 程会引用该变量副本，当同一线程多次重复对字段赋值时,比如：</span></p>
<pre class="brush:java;toolbar:false;">
for(int i=0;i&lt;10;i++)
 a++;</pre>
<p>
	<span style="font-family:helvetica, tahoma, arial, sans-serif;font-size:small">线程有可能只对工作内存中的副本进行赋值,只到最后一次赋值后才同步到主存储区，所以assign,store,weite顺序可以由JVM实现系统决 定。假设有一个共享变量x，线程a执行x=x+1。从上面的描述中可以知道x=x+1并不是一个原子操作，它的执行过程如下：</span><br style="font-family:helvetica, tahoma, arial, sans-serif;font-size:small;line-height:25.200000762939453px;text-align:left;" />
	<span style="font-family: helvetica, tahoma, arial, sans-serif; font-size: small; color: rgb(51, 102, 255); ">1 从主存中读取变量x副本到工作内存<br />
	2 给x加1<br />
	3 将x加1后的值写回主</span><span style="font-family: helvetica, tahoma, arial, sans-serif; font-size: small; "> 存</span><br style="font-family:helvetica, tahoma, arial, sans-serif;font-size:small;line-height:25.200000762939453px;text-align:left;background-color:#efefef;" />
	<span style="font-family: helvetica, tahoma, arial, sans-serif; font-size: small; ">如果另外一个线程b执行x=x-1，执行过程如下：</span><br style="font-family:helvetica, tahoma, arial, sans-serif;font-size:small;line-height:25.200000762939453px;text-align:left;background-color:#efefef;" />
	<span style="font-family: helvetica, tahoma, arial, sans-serif; font-size: small; color: rgb(51, 102, 255); ">1 从主存中读取变量x副本到工作内存<br />
	2 给x减1<br />
	3 将x减1后的值写回主存</span><span style="font-family: helvetica, tahoma, arial, sans-serif; font-size: small; "> </span><br style="font-family:helvetica, tahoma, arial, sans-serif;font-size:small;line-height:25.200000762939453px;text-align:left;background-color:#efefef;" />
	<span style="font-family: helvetica, tahoma, arial, sans-serif; font-size: small; ">那么显然，最终的x的值是不可靠的。假设x现在为10，线程a加1，线程b减1，从表面上看，似乎最终x还是为10，但是多线程情况下会有这种情况发生：</span><br style="font-family:helvetica, tahoma, arial, sans-serif;font-size:small;line-height:25.200000762939453px;text-align:left;background-color:#efefef;" />
	<span style="font-family: helvetica, tahoma, arial, sans-serif; font-size: small; color: rgb(51, 102, 255); ">1：线程a从主存读取x副本到工作内存，工作内存中x值为10<br />
	2：线程b从主存读取x副本到工作内存，工作内存中x值为10<br />
	3：线程a将工作内存中x加1，工作内存中x值为11<br />
	4：线程a将x提交主存中，主存中x为11<br />
	5：线程b将工作内存中x值减1，工作内存中x值为9<br />
	6：线程b将x提交到中主存中，主存中x为9</span><span style="font-family: helvetica, tahoma, arial, sans-serif; font-size: small; "> </span><br style="font-family:helvetica, tahoma, arial, sans-serif;font-size:small;line-height:25.200000762939453px;text-align:left;background-color:#efefef;" />
	<span style="font-family: helvetica, tahoma, arial, sans-serif; font-size: small; ">同样，x有可能为11，如果x是一个银行账户，线程a存款，线程b扣款，显然这样是有严重问题的，要解决这个问题，必须保证线程a和线程b是有序执行的， 并且每个线程执行的加1或减1是一个原子操作。看看下面代码：</span></p>
<p>
	&nbsp;</p>
<pre class="brush:java;toolbar:true;">
public class Account {
      
    private int balance;
      
    public Account(int balance) {
        this.balance = balance;
    }
      
    public int getBalance() {
        return balance;
    }
      
    public void add(int num) {
        balance = balance + num;
    }
      
    public void withdraw(int num) {
        balance = balance - num;
    }
      
    public static void main(String[] args) throws InterruptedException {
        Account account = new Account(1000);
        Thread a = new Thread(new AddThread(account, 20), &quot;add&quot;);
        Thread b = new Thread(new WithdrawThread(account, 20), &quot;withdraw&quot;);
        a.start();
        b.start();
        a.join();
        b.join();
        System.out.println(account.getBalance());
    }
      
    static class AddThread implements Runnable {
        Account account;
        int     amount;
      
        public AddThread(Account account, int amount) {
            this.account = account;
            this.amount = amount;
        }
      
        public void run() {
            for (int i = 0; i &lt; 200000; i++) {
                account.add(amount);
            }
        }
    }
      
    static class WithdrawThread implements Runnable {
        Account account;
        int     amount;
      
        public WithdrawThread(Account account, int amount) {
            this.account = account;
            this.amount = amount;
        }
      
        public void run() {
            for (int i = 0; i &lt; 100000; i++) {
                account.withdraw(amount);
            }
        }
    }
}</pre>
<p>
	<span style="font-size: small; font-family: helvetica, tahoma, arial, sans-serif; line-height: 25.200000762939453px; text-align: left; ">第一次执行结果为10200，第二次执行结果为1060，每次执行的结果都是不确定的，因为线程的执行顺序是不可预见的。这是java同步产生的根 源，synchronized关键字保证了多个线程对于同步块是互斥的，synchronized作为一种同步手段，解决java多线程的执行有序性和内 存可见性，而volatile关键字之解决多线程的内存可见性问题。后面将会详细介绍。</span></p>
<p>
	<strong style="font-size: medium; "><span style="background-color:#ffffff;">synchronized关键字</span></strong><span style="font-size: medium; "><span style="background-color:#ffffff;"> </span></span></p>
<p>
	<span style="font-size:small">上面说了，java用synchronized关键字做为多线程并发环境的执行有序性的保证手段之一。当一段代码会修改共享变量，这一段代码成为互斥区或 临界区，为了保证共享变量的正确性，synchronized标示了临界区。典型的用法如下：</span></p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
synchronized(锁){
     临界区代码
} 
</pre>
<p>
	<span style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; ">为了保证银行账户的安全，可以操作账户的方法如下：</span></p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
public synchronized void add(int num) {
     balance = balance + num;
}
public synchronized void withdraw(int num) {
     balance = balance - num;
}
</pre>
<p>
	<span style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; ">刚才不是说了synchronized的用法是这样的吗：</span></p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
synchronized(锁){
临界区代码
}
</pre>
<p>
	<span style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; ">那么对于public synchronized void add(int num)这种情况，意味着什么呢？其实这种情况，锁就是这个方法所在的对象。同理，如果方法是public&nbsp; static synchronized void add(int num)，那么锁就是这个方法所在的class。</span></p>
<p>
	<span style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; ">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 理论上，每个对象都可以做为锁，但一个对象做为锁时，应该被多个线程共享，这样才显得有意义，在并发环境下，一个没有共享的对象作为锁是没有意义的。假如 有这样的代码：</span></p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
public class ThreadTest{
  public void test(){
     Object lock=new Object();
     synchronized (lock){
        //do something
     }
  }
}
</pre>
<p>
	<span style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; ">lock变量作为一个锁存在根本没有意义，因为它根本不是共享对象，每个线程进来都会执行Object lock=new Object();每个线程都有自己的lock，根本不存在锁竞争。</span></p>
<p>
	<span style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; ">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 每个锁对象都有两个队列，一个是就绪队列，一个是阻塞队列，就绪队列存储了将要获得锁的线程，阻塞队列存储了被阻塞的线程，当一个被线程被唤醒 (notify)后，才会进入到就绪队列，等待cpu的调度。当一开始线程a第一次执行account.add方法时，jvm会检查锁对象account 的就绪队列是否已经有线程在等待，如果有则表明account的锁已经被占用了，由于是第一次运行，account的就绪队列为空，所以线程a获得了锁， 执行account.add方法。如果恰好在这个时候，线程b要执行account.withdraw方法，因为线程a已经获得了锁还没有释放，所以线程 b要进入account的就绪队列，等到得到锁后才可以执行。</span><br style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; background-color: rgb(239, 239, 239); " />
	<span style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; ">一个线程执行临界区代码过程如下：</span><br style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; background-color: rgb(239, 239, 239); " />
	<span style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; ">1 获得同步锁</span><br style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; background-color: rgb(239, 239, 239); " />
	<span style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; ">2 清空工作内存</span><br style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; background-color: rgb(239, 239, 239); " />
	<span style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; ">3 从主存拷贝变量副本到工作内存</span><br style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; background-color: rgb(239, 239, 239); " />
	<span style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; ">4 对这些变量计算</span><br style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; background-color: rgb(239, 239, 239); " />
	<span style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; ">5 将变量从工作内存写回到主存</span><br style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; background-color: rgb(239, 239, 239); " />
	<span style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; ">6 释放锁</span><br style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; background-color: rgb(239, 239, 239); " />
	<span style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; ">可见，synchronized既保证了多线程的并发有序性，又保证了多线程的内存可见性。</span></p>
<p>
	<span style="font-family: Helvetica, Tahoma, Arial, sans-serif; line-height: 25.200000762939453px; text-align: left; "><strong>生产者/消费者模式</strong>&nbsp;</span><br style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; background-color: rgb(239, 239, 239); " />
	<span style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; ">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 生产者/消费者模式其实是一种很经典的线程同步模型，很多时候，并不是光保证多个线程对某共享资源操作的互斥性就够了，往往多个线程之间都是有协作的。</span><br style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; background-color: rgb(239, 239, 239); " />
	<span style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; ">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 假设有这样一种情况，有一个桌子，桌子上面有一个盘子，盘子里只能放一颗鸡蛋，A专门往盘子里放鸡蛋，如果盘子里有鸡蛋，则一直等到盘子里没鸡蛋，B专门 从盘子里拿鸡蛋，如果盘子里没鸡蛋，则等待直到盘子里有鸡蛋。其实盘子就是一个互斥区，每次往盘子放鸡蛋应该都是互斥的，A的等待其实就是主动放弃锁，B 等待时还要提醒A放鸡蛋。</span><br style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; background-color: rgb(239, 239, 239); " />
	<span style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; ">如何让线程主动释放锁</span><br style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; background-color: rgb(239, 239, 239); " />
	<span style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; ">很简单，调用锁的wait()方法就好。wait方法是从Object来的，所以任意对象都有这个方法。看这个代码片段：</span></p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
Object lock=new Object();//声明了一个对象作为锁
   synchronized (lock) {
       balance = balance - num;
       //这里放弃了同步锁，好不容易得到，又放弃了
       lock.wait();
}
</pre>
<p>
	<span style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; ">如果一个线程获得了锁lock，进入了同步块，执行lock.wait()，那么这个线程会进入到lock的阻塞队列。如果调用 lock.notify()则会通知阻塞队列的某个线程进入就绪队列。</span></p>
<p>
	<span style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; ">声明一个盘子，只能放一个鸡蛋</span></p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
import java.util.ArrayList;
import java.util.List;

public class Plate {

    List&lt;Object&gt; eggs = new ArrayList&lt;Object&gt;();

    public synchronized Object getEgg() {
        while(eggs.size() == 0) {
            try {
                wait();
            } catch (InterruptedException e) {
            }
        }

        Object egg = eggs.get(0);
        eggs.clear();// 清空盘子
        notify();// 唤醒阻塞队列的某线程到就绪队列
        System.out.println(&quot;拿到鸡蛋&quot;);
        return egg;
    }

    public synchronized void putEgg(Object egg) {
        while(eggs.size() &gt; 0) {
            try {
                wait();
            } catch (InterruptedException e) {
            }
        }
        eggs.add(egg);// 往盘子里放鸡蛋
        notify();// 唤醒阻塞队列的某线程到就绪队列
        System.out.println(&quot;放入鸡蛋&quot;);
    }
    
    static class AddThread extends Thread{
        private Plate plate;
        private Object egg=new Object();
        public AddThread(Plate plate){
            this.plate=plate;
        }
        
        public void run(){
            for(int i=0;i&lt;5;i++){
                plate.putEgg(egg);
            }
        }
    }
    
    static class GetThread extends Thread{
        private Plate plate;
        public GetThread(Plate plate){
            this.plate=plate;
        }
        
        public void run(){
            for(int i=0;i&lt;5;i++){
                plate.getEgg();
            }
        }
    }
    
    public static void main(String args[]){
        try {
            Plate plate=new Plate();
            Thread add=new Thread(new AddThread(plate));
            Thread get=new Thread(new GetThread(plate));
            add.start();
            get.start();
            add.join();
            get.join();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(&quot;测试结束&quot;);
    }
}
</pre>
<p>
	<span style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; ">&nbsp; 执行结果：</span></p>
<pre class="brush:xhtml;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
放入鸡蛋
拿到鸡蛋
放入鸡蛋
拿到鸡蛋
放入鸡蛋
拿到鸡蛋
放入鸡蛋
拿到鸡蛋
放入鸡蛋
拿到鸡蛋
测试结束
</pre>
<p>
	<span style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; ">声明一个Plate对象为plate，被线程A和线程B共享，A专门放鸡蛋，B专门拿鸡蛋。假设</span></p>
<p>
	<span style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; color: rgb(51, 102, 255); ">1 开始，A调用plate.putEgg方法，此时eggs.size()为0，因此顺利将鸡蛋放到盘子，还执行了notify()方法，唤醒锁的阻塞队列 的线程，此时阻塞队列还没有线程。<br />
	2 又有一个A线程对象调用plate.putEgg方法，此时eggs.size()不为0，调用wait()方法，自己进入了锁对象的阻塞队列。<br />
	3 此时，来了一个B线程对象，调用plate.getEgg方法，eggs.size()不为0，顺利的拿到了一个鸡蛋，还执行了notify()方法，唤 醒锁的阻塞队列的线程，此时阻塞队列有一个A线程对象，唤醒后，它进入到就绪队列，就绪队列也就它一个，因此马上得到锁，开始往盘子里放鸡蛋，此时盘子是 空的，因此放鸡蛋成功。<br />
	4 假设接着来了线程A，就重复2；假设来料线程B，就重复3。</span><span style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; ">&nbsp;</span><br style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; background-color: rgb(239, 239, 239); " />
	<span style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; ">整个过程都保证了放鸡蛋，拿鸡蛋，放鸡蛋，拿鸡蛋。</span></p>
<p>
	<span style="font-family: Helvetica, Tahoma, Arial, sans-serif; line-height: 25.200000762939453px; text-align: left; "><strong>volatile关键字</strong>&nbsp;</span><br style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; background-color: rgb(239, 239, 239); " />
	<span style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; ">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; volatile是java提供的一种同步手段，只不过它是轻量级的同步，为什么这么说，因为volatile只能保证多线程的内存可见性，不能保证多线 程的执行有序性。而最彻底的同步要保证有序性和可见性，例如synchronized。任何被volatile修饰的变量，都不拷贝副本到工作内存，任何 修改都及时写在主存。因此对于Valatile修饰的变量的修改，所有线程马上就能看到，但是volatile不能保证对变量的修改是有序的。什么意思 呢？假如有这样的代码：</span></p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
public class VolatileTest{
  public volatile int a;
  public void add(int count){
       a=a+count;
  }
}
</pre>
<p>
	<span style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; ">&nbsp;当一个VolatileTest对象被多个线程共享，a的值不一定是正确的，因为a=a+count包含了好几步操作，而此时多个线程的执行是无序的，因 为没有任何机制来保证多个线程的执行有序性和原子性。volatile存在的意义是，任何线程对a的修改，都会马上被其他线程读取到，因为直接操作主存， 没有线程对工作内存和主存的同步。所以，volatile的使用场景是有限的，在有限的一些情形下可以使用 volatile 变量替代锁。要使 volatile 变量提供理想的线程安全,必须同时满足下面两个条件:</span><br style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; background-color: rgb(239, 239, 239); " />
	<span style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; color: rgb(51, 102, 255); ">1)对变量的写操作不依赖于当前值。<br />
	2)该变量没有包含在具有其他变量的不变式中</span><span style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; ">&nbsp;</span><br style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; background-color: rgb(239, 239, 239); " />
	<span style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; ">volatile只保证了可见性，所以Volatile适合直接赋值的场景，如：</span></p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
public class VolatileTest{
  public volatile int a;
  public void setA(int a){
      this.a=a;
  }
}
</pre>
<p>
	<span style="font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: small; line-height: 25.200000762939453px; text-align: left; ">在没有volatile声明时，多线程环境下，a的最终值不一定是正确的，因为this.a=a;涉及到给a赋值和将a同步回主存的步骤，这个顺序可能被 打乱。如果用volatile声明了，读取主存副本到工作内存和同步a到主存的步骤，相当于是一个原子操作。所以简单来说，volatile适合这种场 景：一个变量被多个线程共享，线程直接给这个变量赋值。这是一种很简单的同步场景，这时候使用volatile的开销将会非常小。</span></p>
<blockquote>
	<p>
		<span style="font-size:10px;"><span style="font-family: 'courier new', courier, monospace; ">转自：<a href="http://www.iteye.com/topic/806990">http://www.iteye.com/topic/806990</a>&nbsp;作者：<span style="line-height: 25.200000762939453px; text-align: left; ">jameswxx</span></span></span></p>
</blockquote>

