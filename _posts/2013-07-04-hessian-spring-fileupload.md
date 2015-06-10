---
layout: post
title: Hessian4.0.7+Spring3.2.2文件上传
date: 2013-07-04 22:00
author: onecoder
comments: true
categories: [Hessian, hessian4.0.7, spring3, tomcat7]
---
<p>
	最近用Hessian4.0.7做文件上传，先给出自己做试验的样例代码，写在tomcat7下，采用servlet3.0，配置代码如下：</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
**
 * 基于Servlet3.0的，相当于以前&lt;b&gt;web.xml&lt;/b&gt;配置文件的配置类
 * 
 * @author OneCoder
 * @Blog http://www.coderli.com
 * @date 2012-9-30 下午1:16:59
 */
publicclass DefaultWebApplicationInitializer implements
WebApplicationInitializer {

@Override
publicvoid onStartup(ServletContext appContext) throws ServletException {
AnnotationConfigWebApplicationContext rootContext = new AnnotationConfigWebApplicationContext();
rootContext.register(DefaultAppConfig.class);

appContext.addListener(new ContextLoaderListener(rootContext));
ServletRegistration.Dynamic hessianServlet = appContext.addServlet(&quot;hessian&quot;, new HessianServlet());
hessianServlet.addMapping(&quot;/hessian&quot;);
hessianServlet.setInitParameter(&quot;service-class&quot;, HessianFileUploader.class.getName());
//ServletRegistration.Dynamic dispatcher = appContext.addServlet(
//&quot;dispatcher&quot;, new DispatcherServlet(rootContext));
//dispatcher.setLoadOnStartup(1);
//dispatcher.addMapping(&quot;/&quot;);
//// Spring Security 过滤器配置
//FilterRegistration.Dynamic securityFilter = appContext.addFilter(
//&quot;springSecurityFilterChain&quot;, DelegatingFilterProxy.class);
//securityFilter.addMappingForUrlPatterns(null, false, &quot;/*&quot;);
}}
</pre>
<p>
	定义接口，并在服务端实现，Hessian4.0开始支持流作为参数传递，以前则采用byte[]方式传递：</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
public interface FileUploader {

void uplaodFile(String fileName，InputStream is);

}


public class HessianFileUploader implements FileUploader {

@Override
publicvoid uplaodFile(String fileName，InputStream is) {
try {
OutputStream out = new FileOutputStream(&quot;/Users/apple/Desktop/&quot; + fileName);
int nLength = 0;
byte[] bData = newbyte[1024];
while (-1 != (nLength = is.read(bData))) {
out.write(bData, 0, nLength);
}
out.close();
} catch (FileNotFoundException e) {
e.printStackTrace();
} catch (IOException e) {
e.printStackTrace();
} finally {
try {
is.close();
} catch (IOException e) {
e.printStackTrace();}}
}

}
</pre>
<p class="p1">
	客户端调用：</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
publicclass FileUploaderTest {
private FileUploader uploader;
private HessianProxyFactory factory = new HessianProxyFactory();
@Test publicvoid testFileUploader() throws FileNotFoundException, MalformedURLException {
uploader = (FileUploader) factory.create(FileUploader.class, &quot;http://localhost:8080/onecoder-shurnim/hessian&quot;);
InputStream is = new FileInputStream(&quot;/Users/apple/git/onecoder-java/onecoder-shurnim/src/main/resources/logback.xml&quot;); uploader.uplaodFile(is, &quot;logback.xml&quot;); }
 
}</pre>
<p>
	很简单方便。这里曾遇到一个奇怪的问题，如果接口的参数顺序调换，即InputStream在前则会报错：</p>
<blockquote>
	<p>
		com.caucho.hessian.io.HessianProtocolException: uplaodFile: expected string at 0x3c (&lt;)</p>
</blockquote>
<p>
	Hessian协议没有深入研究，不知道是不是一个约定或是要求。开发时需要注意。</p>
<p>
	如果集成spring，只需将servlet交由spring的代理里即可，修改配置即可：</p>
<pre class="brush:xml;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
&lt;!DOCTYPE beans PUBLIC &quot;-//SPRING//DTD BEAN//EN&quot; &quot;http://www.springframework.org/dtd/spring-beans.dtd&quot;&gt; 
&lt;beans&gt;         
&lt;bean id=&quot;defaultHandlerMapping&quot; class=&quot;org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping&quot;/&gt;        
 &lt;bean id=&quot;fileUploader&quot; class=&quot;com.coderli.shurnim.file.HessianFileUploader&quot;/&gt;         
&lt;bean name=&quot;/hello&quot; class=&quot;org.springframework.remoting.caucho.HessianServiceExporter&quot;&gt;                 
     &lt;property name=&quot;service&quot; ref=&quot;fileUploader&quot;/&gt; 
                &lt;property name=&quot;serviceInterface&quot; value=&quot;com.coderli.shurnim.file.FileUploader&quot;/&gt;        
 &lt;/bean&gt; 
&lt;/beans&gt;
</pre>
<p>
	&nbsp;</p>
<p class="p1">
	亲测spring3.2.2+Hessian4.0.7可用。文件内容保存完整。</p>
<p class="p1" style="text-align: center;">
	&nbsp; &nbsp;&nbsp;<img alt="" src="http://onecoder.qiniudn.com/8wuliao/CZ9e0enj/K9Jh4.jpg" style="width: 630px; height: 516px;" /></p>

