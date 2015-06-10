---
layout: post
title: Hibernate的批量处理-批量插入
date: 2012-06-30 21:43
author: onecoder
comments: true
categories: [Hibernate, hibernate, Java, 批量]
---
Hibernate完全以面向对象的方式来操作数据库，当程序里以面向对象的方式操作持久化对象时，将被自动转换为对数据库的操作。例如我们调用Session的delete()方法，来删除持久化对象，Hibernate将负责删除对应的数据记录；当我们执行持久化对象的setter方法时，Hibernate将自动转换为底层的update语句，修改数据库的对应记录。<br />
<br />
问题是：如果我们需要同时更新100000条记录，是不是要逐一加载100000条记录，然后依次调用setter方法&mdash;&mdash;这样不仅烦琐，数据访问的性能也十分糟糕。为了面对这种批量处理的场景，Hibernate提供了批量处理的解决方案。下面分别从批量插入、批量更新和批量删除三个方面介绍如何面对这种批量处理的情形。<br />
<br />
<br />
如果需要将100000条记录插入数据库，通过Hibernate可能会采用如下做法：<br />
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
Session session = sessionFactory.openSession();  
Transaction tx = session.beginTransaction();  
//循环100000次来插入100000条记录  
for ( int i=0; i&lt;100000; i++ )  
{  
    User u = new User (.....);  
    session.save(u);  
}  
tx.commit();  
session.close();  
</pre>
但随着这个程序的运行，总会在某个时候运行失败，并且抛出OutOfMemoryException（内存溢出异常）。这是因为Hibernate的Session持有一个必选的一级缓存，所有的User实例都将在Session级别的缓存区进行了缓存的缘故。<br />
<br />
为了解决这个问题，有个非常简单的思路：定时将Session缓存的数据刷入数据库，而不是一直在Session级别缓存。可以考虑设计一个累加器，每保存一个User实例，累加器增加1。根据累加器的值决定是否需要将Session缓存中的数据刷入数据库。<br />
下面是增加100000个User实例的代码片段。<br />
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
private void addUsers()throws Exception  
{  
    //打开Session  
    Session session = HibernateUtil.currentSession();  
    //开始事务  
    Transaction tx = session.beginTransaction();  
    //循环100000次，插入100000条记录  
    for (int i = 0 ; i &lt; 100000 ; i++ )  
    {  
        //创建User实例  
        User u1 = new User();  
        u1.setName(&quot;xxxxx&quot; + i);  
        u1.setAge(i);  
        u1.setNationality(&quot;china&quot;);  
        //在Session级别缓存User实例  
        session.save(u1);  
        //每当累加器是20的倍数时，将Session中数据刷入数据库，  
        //并清空Session缓存。  
        if (i % 20 == 0)  
        {  
            session.flush();  
            session.clear();  
        }  
    }  
    //提交事务  
    tx.commit();  
    //关闭事务  
    HibernateUtil.closeSession();  
}  
</pre>
<br />
上面的代码中当i % 20 == 0时，手动将Session处缓存的数据写入数据，并且清空Session缓存里的数据。除了要对Session级别缓存进行处理外，还应该通过如下配置来关闭SessionFactory的二级缓存：<br />
<br />
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
hibernate.cache.use_second_level_cache false 
</pre>
否则，即使手动flush Session级别的缓存，但因为在SessionFactory还有二级缓存，也可能引发异常。<br />
<br />
<span style="font-size:14px;"><cite>本文出自 &ldquo;<a href="http://javaligang.blog.51cto.com/">疯狂Java李刚</a>&rdquo; 博客，请务必保留此出<a href="http://javaligang.blog.51cto.com/5026500/910675">http://javaligang.blog.51cto.com/5026500/910675</a></cite></span>
