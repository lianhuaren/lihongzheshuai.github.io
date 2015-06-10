---
layout: post
title: SpringTest框架JUnit单元测试用例获取ApplicationContext实例的方法
date: 2012-10-22 10:51
author: onecoder
comments: true
categories: [applicationContext, junit, springtest, 单元测试]
---
<p>
	JUnit单元测试用例中使用Spring框架，之前我的使用方式很直接。</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
/**
 * 用于需要用到Spring的测试用例基类
 * 
 * @author lihzh
 * @alia OneCoder
 * @blog http://www.coderli.com
 */
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = { &quot;/spring/applicationContext.xml&quot; })
public class SpringTest {}
</pre>
<p>
	在测试的过程中，有人提到，想要获取ApplicationContext实例。于是，添加了对ApplicationContext的注入</p>
<div>
	<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
/**
 * 用于需要用到Spring的测试用例基类
 * 
 * @author lihzh
 * @alia OneCoder
 * @blog http://www.coderli.com
 */
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = { &quot;/spring/applicationContext.xml&quot; })
public class SpringTest {

@Autowired
protected ApplicationContext ctx;
</pre>
</div>
<div>
	其实，Spring中早已直接提供了更加方便使用的基类：AbstractJUnit4SpringContextTests。修改代码如下：</div>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
/**
 * 用于需要用到Spring的测试用例基类
 * 
 * @author lihzh
 * @alia OneCoder
 * @blog http://www.coderli.com
 */
@ContextConfiguration(locations = { &quot;/spring/applicationContext.xml&quot; })
public class SpringTest extends AbstractJUnit4SpringContextTests {

public &lt;T&gt; T getBean(Class&lt;T&gt; type) {
return applicationContext.getBean(type);
}

public Object getBean(String beanName) {
return applicationContext.getBean(beanName);
}

protected ApplicationContext getContext() {
return applicationContext;
}

}
</pre>
<p>
	代码也简洁多了。</p>
<div>
	现在想想，你想要的常用功能，一般人家都能想到了。做之前，不妨先查查有没有现成可用的工具吧：）</div>

