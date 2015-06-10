---
layout: post
title: Spring Batch 初探、使用样例
date: 2014-09-28 18:52
author: onecoder
comments: true
categories: [batch, Java, sample, Spring, Spring]
---
<p>
	工作里用到了Spring Batch项目，以前我也是做并行计算的，听说过Spring Batch不过并没有去具体了解过。今天抽空看看官方文档进行下初步的了解。</p>
<p>
	官方文档地址：<a href="http://docs.spring.io/spring-batch/trunk/reference/html/index.html">http://docs.spring.io/spring-batch/trunk/reference/html/index.html</a></p>
<p style="text-align: center;">
	<img alt="" src="http://onecoder.qiniudn.com/three-layer.png" />&nbsp; &nbsp;&nbsp;</p>
<p>
	层次架构如上图。分三层：应用层，核心层，基础设施层。应用层包括所有的batch任务和用户开发的代码。核心层包括在运行期运行一个任务所需要的类，例如：JobLauncher，Job和Step的实现。应用和核心层都在基础设施层之上，基础设施层包括通用的读写器(readers and writers)以及如RetryTemplate等服务。</p>
<p>
	<strong>Spring Batch中的一些概念：</strong></p>
<p>
	<br />
	<em>Job</em></p>
<p style="text-align: center;">
	<img alt="" src="http://onecoder.qiniudn.com/job.png" /></p>
<p>
	Job是Step的容器，用来定义和配置整个任务的信息：</p>
<ul>
	<li>
		Job的名字</li>
	<li>
		定义Step的顺序</li>
	<li>
		定义任务可否重启</li>
</ul>
<p>
	Job Config Sample：</p>
<pre class="brush:xml;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;" style="width: 1272.03125px;">
&lt;job id=&quot;footballJob&quot;&gt;
    &lt;step id=&quot;playerload&quot; next=&quot;gameLoad&quot;/&gt;
    &lt;step id=&quot;gameLoad&quot; next=&quot;playerSummarization&quot;/&gt;
    &lt;step id=&quot;playerSummarization&quot;/&gt;&lt;/job&gt;
</pre>
<p>
	<em>JobInstance</em></p>
<p>
	是实际运行的Job实例，实例间数据也和业务独立。</p>
<p>
	<em>JobParameters</em></p>
<p style="text-align: center;">
	<img alt="" src="http://onecoder.qiniudn.com/jobParameters.png" /></p>
<p>
	Job运行的参数。。似乎没什么好解释的。</p>
<p>
	<em>JobExecution</em></p>
<p>
	JobExecution是一个技术上的概念，只一次单独的执行任务。执行可以以成功和失败结尾，但是JobInstance只有在JobExecution成功结束的情况下，才被认为是完成的。例如，一个JobInstance第一次执行失败，重新执行，即为另一个JobExecution但是是同一个JobInstance。</p>
<p>
	<em>Step</em></p>
<p>
	一个Job是有一个或者多个Step组成的。Step可复杂可简单。与Job类似，Step也有对应的StepExecution。</p>
<p>
	<br />
	<em>StepExecution</em></p>
<p>
	Step的每一次执行都是即为一个StepExecution。每次Step执行都会创建一个StepExecution。每个execution都包含了相关的Step和JobExecution的引用以及事务相关的数据和起始、结束时间。每个StepExecution也包含了一个ExecutionContext，包含了开发需要持久化的数据。</p>
<p>
	<em>ExecutionContext</em></p>
<p>
	key/value对的对象，用于保存需要记录的上下文信息。scope是包括StepExecution和JobExecution。类似于Quartz中的JobDataMap。可以保存执行过程的信息，用于故障时重新执行和继续执行，甚至状态回滚等等，不过需要用户自行记录。任何时刻，一个StepExecution只会有一个ExecutionContext，由Spring Batch框架负责持久化和保证读取的准确性。</p>
<p>
	另外，需要注意的是，每个JobExectuion都有一个ExecutionContext，每个StepExecution也有一个独立的ExecutionContext。例如：</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;" style="width: 1272.03125px;">
ExecutionContext ecStep = stepExecution.getExecutionContext();
ExecutionContext ecJob = jobExecution.getExecutionContext();
//ecStep does not equal ecJob
</pre>
<p>
	上述代码中，两个ExecutionContext是不相等的，Step中的context是在每个提交点上被保存，而job的context会在两个Step执行之间被保存。</p>
<p>
	<em>JobRepository</em></p>
<p>
	JobRepository是关于前面提到的Job模版(Stereotypes)的持久化机制。提供了对了JobLauncher，Job和Step实现类的CRUD操作。当初次加载Job的时候，从repository中获取 JobExecution，在执行的过程中，StepExecution和JobExectuion的实现类通过repository持久化。</p>
<pre class="brush:xml;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;" style="width: 1272.03125px;">
&lt;job-repository id=&quot;jobRepository&quot;/&gt;
</pre>
<p>
	<em>JobLauncher</em></p>
<p>
	JobLauncher是用于用指定的JobParameters加载Job的接口。</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;" style="width: 1272.03125px;">
public interface JobLauncher {

    public JobExecution run(Job job, JobParameters jobParameters)
                throws JobExecutionAlreadyRunningException, JobRestartException;
}
</pre>
<p>
	Item Reader、 Item Writer、Item Processor</p>
<p style="text-align: center;">
	<img alt="" src="http://onecoder.qiniudn.com/itemReader-writer-processor.png" /></p>
<p>
	分别用于读、写和转换业务数据。转换即是进行数据模型的转换。</p>
<p>
	<em>命名空间（Batch Namesapce）</em></p>
<pre class="brush:xml;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;" style="width: 1272.03125px;">
&lt;beans:beans xmlns=&quot;http://www.springframework.org/schema/batch&quot;
     xmlns:beans=&quot;http://www.springframework.org/schema/beans&quot;
     xmlns:xsi=&quot;http://www.w3.org/2001/XMLSchema-instance&quot;
     xsi:schemaLocation=&quot;
           http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/batch
           http://www.springframework.org/schema/batch/spring-batch-2.2.xsd&quot;&gt;</pre>
<p>
	<strong>完整使用样例：</strong></p>
<p>
	配置文件 batch-context.xml</p>
<pre class="brush:xml;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;" style="width: 1272.03125px;">
&lt;beans:beans xmlns= &quot;http://www.springframework.org/schema/batch&quot;
     xmlns:beans=&quot;http://www.springframework.org/schema/beans&quot; xmlns:xsi= &quot;http://www.w3.org/2001/XMLSchema-instance&quot;
     xsi:schemaLocation=&quot;
           http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/batch
           http://www.springframework.org/schema/batch/spring-batch-2.2.xsd&quot;&gt;
     &lt;beans:bean id =&quot;first-tasklet&quot;
           class= &quot;com.coderli.spring.batch.firstjob.FirstTasklet&quot; &gt;&lt;/beans:bean &gt;

     &lt;beans:bean id =&quot;jobRepository&quot;
           class= &quot;org.springframework.batch.core.repository.support.MapJobRepositoryFactoryBean&quot; &gt;
           &lt;beans:property name =&quot;transactionManager&quot; ref= &quot;transactionManager&quot; /&gt;
     &lt;/beans:bean &gt;

     &lt;beans:bean id =&quot;transactionManager&quot;
           class= &quot;org.springframework.batch.support.transaction.ResourcelessTransactionManager&quot; &gt;&lt;/beans:bean &gt;

     &lt;job id =&quot;onecoder-job&quot;&gt;
           &lt;step id =&quot;first-step&quot; next=&quot;secend-step&quot;&gt;
               &lt;tasklet ref =&quot;first-tasklet&quot;&gt;&lt;/ tasklet&gt;
           &lt;/step &gt;
           &lt;step id =&quot;secend-step&quot; &gt;
               &lt;tasklet &gt;
                    &lt;chunk reader =&quot;myReader&quot; writer= &quot;myWriter&quot; processor =&quot;myProcessor&quot;
                         commit-interval= &quot;1&quot;&gt;&lt;/chunk &gt;
               &lt;/tasklet &gt;
           &lt;/step &gt;
     &lt;/job &gt;

     &lt;beans:bean id =&quot;myReader&quot; class= &quot;com.coderli.spring.batch.firstjob.MyReader&quot; &gt;&lt;/beans:bean &gt;
     &lt;beans:bean id =&quot;myWriter&quot; class= &quot;com.coderli.spring.batch.firstjob.MyWriter&quot; &gt;&lt;/beans:bean &gt;
     &lt;beans:bean id =&quot;myProcessor&quot;
           class= &quot;com.coderli.spring.batch.firstjob.MyProcessor&quot; &gt;&lt;/beans:bean &gt;
     &lt;beans:bean id =&quot;myFirstJobLauncher&quot;
           class= &quot;org.springframework.batch.core.launch.support.SimpleJobLauncher&quot; &gt;
           &lt;beans:property name =&quot;taskExecutor&quot; ref= &quot;syncTaskExecutor&quot; /&gt;
           &lt;beans:property name =&quot;jobRepository&quot; ref= &quot;jobRepository&quot; /&gt;
     &lt;/beans:bean &gt;
     &lt;beans:bean id =&quot;syncTaskExecutor&quot;
           class= &quot;org.springframework.core.task.SyncTaskExecutor&quot; /&gt;
     &lt;beans:bean id =&quot;jobLauncherTestUtils&quot;
           class= &quot;org.springframework.batch.test.JobLauncherTestUtils&quot; &gt;
           &lt;beans:property name =&quot;job&quot; ref= &quot;onecoder-job&quot;&gt;&lt;/beans:property &gt;
     &lt;/beans:bean &gt;

&lt;/beans:beans&gt;
</pre>
<p>
	<em>First-Tasklet类</em></p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;" style="width: 1272.03125px;">
package com.coderli.spring.batch.firstjob;

import lombok.extern.slf4j.Slf4j;

import org.springframework.batch.core.StepContribution;
import org.springframework.batch.core.scope.context.ChunkContext;
import org.springframework.batch.core.step.tasklet.Tasklet;
import org.springframework.batch.repeat.RepeatStatus;

@Slf4j
public class FirstTasklet implements Tasklet {

     @Override
     public RepeatStatus execute(StepContribution contribution,
              ChunkContext chunkContext) throws Exception {
           log.info( &quot;This is tasklet one in step one of job MyJob&quot;);
           return RepeatStatus.FINISHED;
     }
}
</pre>
<p>
	Reader类：</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;" style="width: 1272.03125px;">
package com.coderli.spring.batch.firstjob;

import lombok.extern.slf4j.Slf4j;

import org.springframework.batch.item.ItemReader;
import org.springframework.batch.item.NonTransientResourceException;
import org.springframework.batch.item.ParseException;
import org.springframework.batch.item.UnexpectedInputException;

/**
* 自定义Reader类
*
* @author OneCoder
* @date 2014年9月28日 下午2:33:43
*/
@Slf4j
public class MyReader implements ItemReader&lt;MyModel&gt; {

     private int count;

     @Override
     public MyModel read() throws Exception, UnexpectedInputException,
              ParseException, NonTransientResourceException {
           log.info( &quot;This is my reader in step two of job: [MyJob.]&quot;);
          MyModel model = null;
           if ( count &lt; 2) {
               model = new MyModel();
               model.setDescription( &quot;My Description&quot;);
               model.setId( &quot;My ID&quot;);
               model.setName( &quot;My Name&quot;);
               count++;
          }
           return model;
     }

}
</pre>
<p>
	writer类</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;" style="width: 1272.03125px;">
package com.coderli.spring.batch.firstjob;

import java.util.List;

import lombok.extern.slf4j.Slf4j;

import org.springframework.batch.item.ItemWriter;

/**
* 自定义Writer类
*
* @author OneCoder
* @date 2014年9月28日 下午2:46:20
*/
@Slf4j
public class MyWriter implements ItemWriter&lt;String&gt; {

     @Override
     public void write(List&lt;? extends String&gt; items) throws Exception {
           log.info( &quot;This is my writer in step two for job: [MyJob].&quot;);
           log.info( &quot;Write the JSON string to the console.&quot;);
           for (String item : items) {
               log.info( &quot;Write item: {}&quot;, item);
          }
     }

}
</pre>
<p>
	processor类</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;" style="width: 1272.03125px;">
package com.coderli.spring.batch.firstjob;

import lombok.extern.slf4j.Slf4j;

import org.springframework.batch.item.ItemProcessor;

import com.google.gson.Gson;

/**
*
* @author OneCoder
* @date 2014年9月28日 下午2:39:48
*/
@Slf4j
public class MyProcessor implements ItemProcessor&lt;MyModel, String&gt; {

     @Override
     public String process(MyModel item) throws Exception {
           log.info( &quot;This is my process in step two of job: [MyJob].&quot;);
           log.info( &quot;Transfer MyModel to JSON string.&quot;);
          Gson gson = new Gson();
           return gson.toJson( item);
     }
}
</pre>
<p>
	测试类</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;" style="width: 1272.03125px;">
package com.coderli.spring.batch.firstjob;

import org.junit.Assert;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.batch.core.JobExecution;
import org.springframework.batch.test.JobLauncherTestUtils;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

/**
* 任务启动器，通过JUnit测试方式启动
*
* @author OneCoder
* @date 2014年9月28日 下午3:03:03
*/
@RunWith(SpringJUnit4ClassRunner. class)
@ContextConfiguration(locations = { &quot;../batch-context.xml&quot; })
public class MyFirstJobTest {

     @Autowired
     private JobLauncherTestUtils jobLauncherTestUtils;


     @Test
     public void testJob() throws Exception {
           jobLauncherTestUtils.launchJob();
          JobExecution jobExecution = jobLauncherTestUtils .launchJob();
          Assert. assertEquals(&quot;COMPLETED&quot;, jobExecution.getExitStatus().getExitCode());
     }
}

</pre>
<p>
	简单介绍上述代码里用到的东西，@SLF4J注解是lombok中的，之前介绍过。测试用的Spring-test和spring-batch-test提供的相关功能。</p>

