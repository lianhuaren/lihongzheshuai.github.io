---
layout: post
title: Spring源码学习-FileSystemXmlApplicationContext路径格式及解析方式
date: 2012-06-09 20:21
author: onecoder
comments: true
categories: [Java, Spring, Spring, SSH, 源码]
---
了解完了FileSystemXmlApplicationContext构造函数，我们回到<span style="color: #0000ff;"><a href="http://www.coderli.com/archives/spring-filexml-constructor/" title="[原创] Spring研究-容器初始化之FileSystemXmlApplicationContext构造函数"><span style="color: #0000ff;">上节</span></a></span>留下的思考的问题：
<ol>
	<li>
		支持路径格式的研究。(绝对？相对？通配符？classpath格式又如何？)</li>
	<li>
		路径如何解析？</li>
</ol>
<div>
	下面，我们就来一一验证和解答。</div>
<div>
	先放出本次测试用的配置文件(app-context和test.properties)：</div>
<div>
	<pre class="brush:xml;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
&lt;bean id=&quot;placeHolderConfig&quot;
 class=&quot;org.springframework.beans.factory.config.PropertyPlaceholderConfigurer&quot;&gt;
 &lt;property name=&quot;systemPropertiesModeName&quot; value=&quot;SYSTEM_PROPERTIES_MODE_OVERRIDE&quot; /&gt; 
 &lt;property name=&quot;locations&quot;&gt;
 &lt;list&gt;
 &lt;value&gt;classpath*:spring/test.properties&lt;/value&gt;
 &lt;/list&gt;
 &lt;/property&gt;
 &lt;/bean&gt;
 &lt;bean id=&quot;veryCommonBean&quot; class=&quot;kubi.coder.bean.VeryCommonBean&quot;&gt;
 &lt;property name=&quot;name&quot; value=&quot;${test.name}&quot;&gt;&lt;/property&gt;
 &lt;/bean&gt;
</pre>
</div>
<div>
	<span style="color: #ff0000;">test.properties</span></div>
<div>
	<pre class="brush:xml;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
test.name=verycommonbean-name
</pre>
</div>
<div>
	首先想到的自然是最普通的绝对路径：</div>
<div>
	<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
/**
  * 测试通过普通的绝对路径:
  * &lt;p&gt;D:\\workspace-home\\spring-custom\\src\\main\\resources\\spring\\app-context.xml&lt;/p&gt;
  * 读取配置文件
  * 
  * @author lihzh
  * @date 2012-5-5 上午10:53:53
  */
 @Test
 public void testPlainAbsolutePath() {
 String path = &quot;D:\\workspace-home\\spring-custom\\src\\main\\resources\\spring\\app-context.xml&quot;;
 ApplicationContext appContext = new FileSystemXmlApplicationContext(path);
 assertNotNull(appContext);
 VeryCommonBean bean = appContext.getBean(VeryCommonBean.class);
 assertNotNull(bean);
 assertEquals(&quot;verycommonbean-name&quot;, bean.getName());
 }
</pre>
</div>
<div>
	测试通过，我们来看下Spring是怎么找到该文件的。之前已经说过refresh这个函数，是Spring生命周期的开始，我们就以它为入口，顺藤摸瓜，时序图如下：</div>
<div>
	<a href="http://www.coderli.com/wp-content/uploads/2012/06/springsequence-filepath.jpg"><img alt="spring路径解析时序图" class="alignnone size-large wp-image-349" height="318" src="http://www.coderli.com/wp-content/uploads/2012/06/springsequence-filepath-1024x552.jpg" title="springsequence-filepath" width="590" /></a></div>
<div>
	最终，我们找到解析路径的关键方法，PathMatchingResourcePatternResolver的getResources方法和DefaultResourceLoader中的getResource方法：</div>
<div>
	<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
public Resource[] getResources(String locationPattern) throws IOException {
 Assert.notNull(locationPattern, &quot;Location pattern must not be null&quot;);
 if (locationPattern.startsWith(CLASSPATH_ALL_URL_PREFIX)) {
 // a class path resource (multiple resources for same name possible)
 if (getPathMatcher().isPattern(locationPattern.substring(CLASSPATH_ALL_URL_PREFIX.length()))) {
 // a class path resource pattern
 return findPathMatchingResources(locationPattern);
 }
 else {
 // all class path resources with the given name
 return findAllClassPathResources(locationPattern.substring(CLASSPATH_ALL_URL_PREFIX.length()));
 }
 }
 else {
 // Only look for a pattern after a prefix here
 // (to not get fooled by a pattern symbol in a strange prefix).
 int prefixEnd = locationPattern.indexOf(&quot;:&quot;) + 1;
 if (getPathMatcher().isPattern(locationPattern.substring(prefixEnd))) {
 // a file pattern
 return findPathMatchingResources(locationPattern);
 }
 else {
 // a single resource with the given name
 return new Resource[] {getResourceLoader().getResource(locationPattern)};
 }
 }
 }
</pre>
</div>
<div>
	<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
public Resource getResource(String location) {
 Assert.notNull(location, &quot;Location must not be null&quot;);
 if (location.startsWith(CLASSPATH_URL_PREFIX)) {
 return new ClassPathResource(location.substring(CLASSPATH_URL_PREFIX.length()), getClassLoader());
 }
 else {
 try {
 // Try to parse the location as a URL...
 URL url = new URL(location);
 return new UrlResource(url);
 }
 catch (MalformedURLException ex) {
 // No URL -&gt; resolve as resource path.
 return getResourceByPath(location);
 }
 }
 }
</pre>
</div>
<div>
	<div>
		其中常量</div>
	<div>
		<span style="color: #ff0000;">CLASSPATH_ALL_URL_PREFIX =&nbsp;</span>&quot;classpath*:&quot;;</div>
	<div>
		<span style="color: #ff0000;">CLASSPATH_URL_PREFIX</span>&nbsp;=&nbsp;&quot;classpath:&quot;；</div>
	<div>
		我们输入的路径是绝对路径：&quot;D:\\workspace-home\\spring-custom\\src\\main\\resources\\spring\\app-context.xml&quot;。不是以classpath*开头的，所以会落入else之中。在else中：getPathMatcher().isPattern()，实际是调用AntPathMatcher中的isPattern()方法：</div>
	<div>
		<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
public boolean isPattern(String path) {
		return (path.indexOf(&#39;*&#39;) != -1 || path.indexOf(&#39;?&#39;) != -1);
	}
</pre>
	</div>
	<div>
		<div>
			是用来判断&quot;:&quot;以后的路径中是否包含通配符&ldquo;*&rdquo;或者 &quot;?&quot;。</div>
		<div>
			我们的路径显然也不包含，所以最终会直接走入getResource方法。</div>
		<div>
			仍然，路径既不是以classpath开头的，也不是URL格式的路径，所以最终会落入 getResourceByPath(location)这个分支，而我们之前介绍过，这个方法恰好是在FileSystemXmlApplicationContext这个类中复写过的：</div>
		<div>
			<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
protected Resource getResourceByPath(String path) {
 if (path != null &amp;&amp; path.startsWith(&quot;/&quot;)) {
 path = path.substring(1);
 }
 return new FileSystemResource(path);
 }
</pre>
		</div>
	</div>
</div>
<div>
	我们给的路径不是以&quot;/&quot;开头，所以直接构造了一个FileSystemResource:</div>
<div>
	<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
public FileSystemResource(String path) {
 Assert.notNull(path, &quot;Path must not be null&quot;);
 this.file = new File(path);
 this.path = StringUtils.cleanPath(path);
 }
</pre>
</div>
<div>
	&nbsp;</div>
<div>
	<div>
		即用路径直接构造了一个File。这里StringUtil.cleanPath方法：</div>
	<div>
		主要是将传入的路径规范化，比如将windows的路径分隔符&ldquo;\\&rdquo;替换为标准的&ldquo;/&ldquo;，如果路径中含有.(当前文件夹)，或者..(上层文件夹)，则计算出其真实路径。而File本身是支持这样的路径的，也就是说，spring可以支持这样的路径。出于好奇，我们也针对这个方法测试如下：</div>
	<div>
		<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
/**
  * 测试通过含有.或者..的绝对路径
  * &lt;p&gt;D:\\workspace-home\\spring-custom\\.\\src\\main\\resources\\spring\\..\\spring\\app-context.xml&lt;/p&gt;
  * 读取配置文件
  * 
  * @author lihzh
  * @date 2012-5-5 上午10:53:53
  */
 @Test
 public void testContainDotAbsolutePath() {
 String path = &quot;D:\\workspace-home\\spring-custom\\.\\src\\main\\resources\\spring\\..\\spring\\app-context.xml&quot;;
 ApplicationContext appContext = new FileSystemXmlApplicationContext(path);
 assertNotNull(appContext);
 VeryCommonBean bean = appContext.getBean(VeryCommonBean.class);
 assertNotNull(bean);
 assertEquals(&quot;verycommonbean-name&quot;, bean.getName());
 }
</pre>
	</div>
</div>
<div>
	&nbsp;</div>
<div>
	<div>
		容器可以正常初始化。路径计算正确。</div>
	<div>
		&nbsp;</div>
	<div>
		<span style="color: #ff0000;">补充说明：Spring最终读取配置文件，是通过InputStream加载的，Spring中的各种Resource的最上层接口InputStreamResource中定义了唯一的一个方法getInputStream。也就是说，只要保证各Resource的实现类的getInputStream方法能够正常获取流，Spring容器即可解析初始化。对于FileSystemResource而已，其实现如下：</span></div>
	<div>
		<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
/**
  * This implementation opens a FileInputStream for the underlying file.
  * @see java.io.FileInputStream
  */
 public InputStream getInputStream() throws IOException {
 return new FileInputStream(this.file);
 }
</pre>
	</div>
</div>
<div>
	<div>
		所以，我们说，此时只有是File正常支持的格式，Spring才能正常初始化。</div>
	<div>
		&nbsp;</div>
	<div>
		继续回到前面的话题。我们目前只验证else分支中的catch分支。根据代码分析，即使是FileSystemXmlApplicationContext也可以支持Classpath格式的路径和URL格式的路径的。验证如下：</div>
	<div>
		<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
/**
  * 测试通过含有.或者..的绝对路径
  * &lt;p&gt;file:/D:\\workspace-home\\spring-custom\\src\\main\\resources\\spring\\app-context.xml&lt;/p&gt;
  * 读取配置文件
  * 
  * @author lihzh
  * @date 2012-5-5 上午10:53:53
  */
 @Test
 public void testURLAbsolutePath() {
 String path = &quot;file:/D:\\workspace-home\\spring-custom\\src\\main\\resources\\spring\\app-context.xml&quot;;
 ApplicationContext appContext = new FileSystemXmlApplicationContext(path);
 assertNotNull(appContext);
 VeryCommonBean bean = appContext.getBean(VeryCommonBean.class);
 assertNotNull(bean);
 assertEquals(&quot;verycommonbean-name&quot;, bean.getName());
 }
 
 /**
  * 测试通过Classpath类型的路径
  * &lt;p&gt;classpath:spring/app-context.xml&lt;/p&gt;
  * 通过读取配置文件
  * 
  * @author lihzh
  * @date 2012-5-5 上午10:53:53
  */
 @Test
 public void testClassPathStylePath() {
 String path = &quot;classpath:spring/app-context.xml&quot;;
 ApplicationContext appContext = new FileSystemXmlApplicationContext(path);
 assertNotNull(appContext);
 VeryCommonBean bean = appContext.getBean(VeryCommonBean.class);
 assertNotNull(bean);
 assertEquals(&quot;verycommonbean-name&quot;, bean.getName());
 }

</pre>
	</div>
</div>
<div>
	&nbsp;</div>
<div>
	验证通过，并且通过debug确认，确实走入了相应的分支，分别构造了UrlResource和ClassPathResource实例。所以，之后Spring会分别调用这个两个Resource中的getInputStream方法获取流，解析配置文件。附上这两个类中的getInputStream方法，有兴趣的可以继续研究：</div>
<div>
	<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
 /**
  * This implementation opens an InputStream for the given URL.
  * It sets the &quot;UseCaches&quot; flag to &lt;code&gt;false&lt;/code&gt;,
  * mainly to avoid jar file locking on Windows.
  * @see java.net.URL#openConnection()
  * @see java.net.URLConnection#setUseCaches(boolean)
  * @see java.net.URLConnection#getInputStream()
  */
 public InputStream getInputStream() throws IOException {
 URLConnection con = this.url.openConnection();
 ResourceUtils.useCachesIfNecessary(con);
 try {
 return con.getInputStream();
 }
 catch (IOException ex) {
 // Close the HTTP connection (if applicable).
 if (con instanceof HttpURLConnection) {
 ((HttpURLConnection) con).disconnect();
 }
 throw ex;
 }
 }

        /**
  * This implementation opens an InputStream for the given class path resource.
  * @see java.lang.ClassLoader#getResourceAsStream(String)
  * @see java.lang.Class#getResourceAsStream(String)
  */
 public InputStream getInputStream() throws IOException {
 InputStream is;
 if (this.clazz != null) {
 is = this.clazz.getResourceAsStream(this.path);
 }
 else {
 is = this.classLoader.getResourceAsStream(this.path);
 }
 if (is == null) {
 throw new FileNotFoundException(
 getDescription() + &quot; cannot be opened because it does not exist&quot;);
 }
 return is;
 }
</pre>
</div>
<div>
	<div>
		上述两个实现所属的类，我想应该一目了然吧~~</div>
	<div>
		&nbsp;</div>
	<div>
		至此，我们算是分析验证通过了一个小分支下的支持的路径的情况，其实，这只是这些都是最简单直接的。回想刚才的分析，<span style="color: #ff0000;">如果路径包含通配符(?,*)spring是怎么处理的？如果是以classpath*开头的又是如何呢？</span>？鉴于害怕文章过长，我们下回分解&hellip;&hellip;&hellip;&hellip;o(&cap;_&cap;)o</div>
</div>

