---
layout: post
title: Spring研究-容器初始化之FileSystemXmlApplicationContext构造函数
date: 2012-06-07 12:32
author: onecoder
comments: true
categories: [Spring, Spring, SSH, 源码]
---
<div>
	宅男Coder，没有其他爱好，闲暇之余抱着瞻仰的心态去阅读一下Spring的源码，期许能收获一支半解。要学习Spring的源码，第一步自然是下载和编译Spring的源码，这个我在之前的博文中已经发表过了。具体可参考：</div>
<span style="color: #0000ff;"><a href="http://www.coderli.com/archives/springframework-source-compile/" title="[原创] SpringFramework3 源码下载和编译教程"><span style="color: #0000ff;">《<span style="font-family: 'Microsoft Yahei';">SpringFramework源码下载和编译教程</span>》</span></a></span>
<div>
	面对茫茫多的Spring的工程和代码，很多人可能会无从下手。其实想想，Spring也是有入口的，那就是配置文件的加载。Spring容器的构建完全是基于配置文件的配置的。不论是Web工程，还是普通的Java应用，加载Spring配置文件都是首要的工作。所以，我就从配置文件的加载学起。</div>
<div>
	&nbsp;</div>
<div>
	要加载配置文件，首先当然是要找到该文件。大多数人通常都是在Web应用中使用Spring。网上搜搜配置，配置文件的名字就叫约定的：applicationContext.xml，然后往编译路径下一扔，Spring自然就好用了，就没过多的关注过其他容器初始化的问题。其实，一个自然应该想到的问题就是：一个普通的J2SE应用该如何使用Spring呢？答案很简单：new 出一个ApplicationContext的实例就好了。例如：</div>
<div>
	<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
private static final String SPRINT_FILEPATH_CONTEXT = &quot;D:\\workspace-home\\OpenSourceStudy\\src\\main\\resources\\spring\\app-context.xml&quot;;          

ApplicationContext  appContext = new FileSystemXmlApplicationContext(
				SPRINT_FILEPATH_CONTEXT);
</pre>
</div>
<div>
	只需上述一行代码，一个基于指定的配置文件的Spring容器就初始化完成了。
	<div>
		&nbsp;</div>
	<div>
		下面，我们来仔细看看FileSystemXmlApplicationContext这个类：</div>
	<div>
		<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
public class FileSystemXmlApplicationContext extends AbstractXmlApplicationContext {

	/**
	 * Create a new FileSystemXmlApplicationContext for bean-style configuration.
	 * @see #setConfigLocation
	 * @see #setConfigLocations
	 * @see #afterPropertiesSet()
	 */
	public FileSystemXmlApplicationContext() {
	}

	/**
	 * Create a new FileSystemXmlApplicationContext for bean-style configuration.
	 * @param parent the parent context
	 * @see #setConfigLocation
	 * @see #setConfigLocations
	 * @see #afterPropertiesSet()
	 */
	public FileSystemXmlApplicationContext(ApplicationContext parent) {
		super(parent);
	}

	/**
	 * Create a new FileSystemXmlApplicationContext, loading the definitions
	 * from the given XML file and automatically refreshing the context.
	 * @param configLocation file path
	 * @throws BeansException if context creation failed
	 */
	public FileSystemXmlApplicationContext(String configLocation) throws BeansException {
		this(new String[] {configLocation}, true, null);
	}

	/**
	 * Create a new FileSystemXmlApplicationContext, loading the definitions
	 * from the given XML files and automatically refreshing the context.
	 * @param configLocations array of file paths
	 * @throws BeansException if context creation failed
	 */
	public FileSystemXmlApplicationContext(String... configLocations) throws BeansException {
		this(configLocations, true, null);
	}

	/**
	 * Create a new FileSystemXmlApplicationContext with the given parent,
	 * loading the definitions from the given XML files and automatically
	 * refreshing the context.
	 * @param configLocations array of file paths
	 * @param parent the parent context
	 * @throws BeansException if context creation failed
	 */
	public FileSystemXmlApplicationContext(String[] configLocations, ApplicationContext parent) throws BeansException {
		this(configLocations, true, parent);
	}

	/**
	 * Create a new FileSystemXmlApplicationContext, loading the definitions
	 * from the given XML files.
	 * @param configLocations array of file paths
	 * @param refresh whether to automatically refresh the context,
	 * loading all bean definitions and creating all singletons.
	 * Alternatively, call refresh manually after further configuring the context.
	 * @throws BeansException if context creation failed
	 * @see #refresh()
	 */
	public FileSystemXmlApplicationContext(String[] configLocations, boolean refresh) throws BeansException {
		this(configLocations, refresh, null);
	}

	/**
	 * Create a new FileSystemXmlApplicationContext with the given parent,
	 * loading the definitions from the given XML files.
	 * @param configLocations array of file paths
	 * @param refresh whether to automatically refresh the context,
	 * loading all bean definitions and creating all singletons.
	 * Alternatively, call refresh manually after further configuring the context.
	 * @param parent the parent context
	 * @throws BeansException if context creation failed
	 * @see #refresh()
	 */
	public FileSystemXmlApplicationContext(String[] configLocations, boolean refresh, ApplicationContext parent)
			throws BeansException {

		super(parent);
		setConfigLocations(configLocations);
		if (refresh) {
			refresh();
		}
	}


	/**
	 * Resolve resource paths as file system paths.
	 * &lt;p&gt;Note: Even if a given path starts with a slash, it will get
	 * interpreted as relative to the current VM working directory.
	 * This is consistent with the semantics in a Servlet container.
	 * @param path path to the resource
	 * @return Resource handle
	 * @see org.springframework.web.context.support.XmlWebApplicationContext#getResourceByPath
	 */
	@Override
	protected Resource getResourceByPath(String path) {
		if (path != null &amp;&amp; path.startsWith(&quot;/&quot;)) {
			path = path.substring(1);
		}
		return new FileSystemResource(path);
	}

}
</pre>
	</div>
</div>
<div>
	一共七个构造函数和一个复写的方法。我们现在重点关注构造函数，除前两个之外，其他的构造函数都最终指向构造函数：</div>
<div>
	<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
public FileSystemXmlApplicationContext(String[] configLocations, boolean refresh, ApplicationContext parent)
			throws BeansException {

		super(parent);
		setConfigLocations(configLocations);
		if (refresh) {
			refresh();
		}
</pre>
</div>
<div>
	&nbsp;</div>
<div>
	<div>
		只是对没有传入的参数，给了一个默认值。</div>
	<div>
		该构造函数有三个参数：</div>
	<div>
		<ul style="list-style-type: disc;">
			<li>
				String[] configLocations - 配置文件的路径数组。是数组也就是说，支持同时传入多个配置文件路径。</li>
			<li>
				boolean refresh - 是否刷新，如果是true，则会开始初始化Spring容器， false则暂时不初始化Spring容器。</li>
			<li>
				&nbsp;ApplicationContext parent - Spring容器上下文。即可以传入已经初始化过的Spring容器，新初始化的容器会包含parent上下文中的内容，例如：父容器中定义的bean等。</li>
		</ul>
		<span style="color: #ff0000;">注</span>：refresh()方法可谓Spring的核心的入口函数，Spring容器的初始化正是由此开始。一些Spring学习的书中，也向学习Spring的读者推荐，如果感到无所适从，可从该方法入手，研究Spring的整个生命周期。后续我们也会从该方法入手重点研究。现在只需理解，其为Spring容器初始化的一个&ldquo;开关&rdquo;即可。
		<div>
			&nbsp;</div>
		前两个构造函数与该构造函数最大的区别就是，没有调用refresh函数。也就是说，Spring容器，此时并未初始化。此时如果用getBean方法去获取Bean的实例，会报容器并未初始化的异常。
		<div>
			&nbsp;</div>
		<div>
			下面给出一些关于构造函数的测试用例，能更直观、具体的说明问题：</div>
		<div>
			<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
private String filePath = &quot;D:\\workspace-home\\spring-custom\\src\\main\\resources\\spring\\app-context.xml&quot;;
	private String parentFilePath = &quot;D:\\workspace-home\\spring-custom\\src\\main\\resources\\spring\\parent-context.xml&quot;;

	/**
	 * 直接指定一个单一的配置文件初始化Spring容器。容器中包含该指定配置文件中定义的Bean{@code VeryCommonBean}。
	 * 
	 * @author lihzh
	 * @date 2012-4-29 下午9:39:27
	 */
	@Test
	public void testFileContextWithPath() {
		ApplicationContext appContext = new FileSystemXmlApplicationContext(
				filePath);
		assertNotNull(&quot;The app context should not be null.&quot;, appContext);
		assertNotNull(appContext.getBean(VeryCommonBean.class));
	}

	/**
	 * 将已经初始化好的Spring容器上下文传递给新的Spring容器。需手动调用容器的刷新
	 * {@code ConfigurableApplicationContext#refresh()}(初始化)。
	 * 
	 * @author lihzh
	 * @date 2012-5-4 下午9:28:40
	 */
	@Test
	public void testFileContextWithParentOnly() {
		ApplicationContext parentContext = new FileSystemXmlApplicationContext(
				parentFilePath);
		ConfigurableApplicationContext appContext = new FileSystemXmlApplicationContext(
				parentContext);
		assertNotNull(&quot;The parent context should not be null.&quot;, parentContext);
		assertNotNull(appContext);
		// 需要手动刷新(初始化)容器
		appContext.refresh();
		assertNotNull(parentContext.getBean(ParentBean.class));
		assertNotNull(appContext.getBean(ParentBean.class));
	}

	/**
	 * 根据指定的配置文件和已经初始化好的Parent容器上下文，实例化一个新的Spring容器，该容器中包含这两部分的信息。
	 * 
	 * @author lihzh
	 * @date 2012-5-4 下午10:07:20
	 */
	@Test
	public void testFileContextWithParentAndItself() {
		ApplicationContext parentContext = new FileSystemXmlApplicationContext(
				parentFilePath);
		ApplicationContext appContext = new FileSystemXmlApplicationContext(
				new String[] { filePath }, parentContext);
		assertNotNull(&quot;The parent context should not be null&quot;, parentContext);
		assertNotNull(appContext);
		assertTrue(&quot;Should not have bean:[VeryCommonBean] in parent context.&quot;,
				!parentContext.containsBean(&quot;VeryCommonBean&quot;));
		assertNotNull(parentContext.getBean(ParentBean.class));
		assertNotNull(appContext.getBean(ParentBean.class));
		assertNotNull(&quot;Should have bean:[VeryCommonBean].&quot;,
				appContext.getBean(&quot;VeryCommonBean&quot;));
	}

	/**
	 * 同时指定多个配置文件来初始化Spring容器
	 * 
	 * @author lihzh
	 * @date 2012-5-4 下午10:09:02
	 */
	@Test
	public void testFileContextWithMultiPath() {
		ApplicationContext appContext = new FileSystemXmlApplicationContext(
				parentFilePath, filePath);
		assertNotNull(appContext);
		assertNotNull(appContext.getBean(ParentBean.class));
		assertNotNull(&quot;Should have bean:[VeryCommonBean].&quot;,
				appContext.getBean(VeryCommonBean.class));
	}
</pre>
		</div>
	</div>
	<div>
		&nbsp;</div>
	<div>
		<div>
			<span style="color: #ff0000;">了解这些有什么用？</span></div>
		<div>
			<ol>
				<li>
					学会在一个普通的J2SE应用中使用Spring，初始化Spring容器。</li>
				<li>
					了解初始化Spring容器的几种方式和传入的参数，可配合不同的场景使用。例如：在程序运行期动态初始化一个新的Spring容器，包含已经初始化的容器，即可用与parent相关的构造函数。再例如：在可能的特定的场景下，Spring容器需要在特定的实际初始化，可先调用空构造函数，再传入location，再手动refresh。或者直接给refresh传入false，再手动refresh。再比如：配合Spring的foo包，可监控Spring配置文件的变化，利用refresh函数，实时更新Spring容器等等。</li>
				<li>
					其他读者可自行发挥想象~~</li>
			</ol>
			<div>
				<span style="color: #ff0000;">下期留待思考的：</span></div>
		</div>
		<div>
			<ol>
				<li>
					支持路径格式的研究。(绝对？相对？通配符？classpath格式又如何？)</li>
				<li>
					路径如何解析？</li>
			</ol>
		</div>
	</div>
</div>

