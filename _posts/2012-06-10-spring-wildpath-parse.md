---
layout: post
title: Spring源码学习-含有通配符路径解析（上）
date: 2012-06-10 21:07
author: onecoder
comments: true
categories: [Spring, Spring, SSH, 源码]
---
继续前文<span style="color: #0000ff;"><a href="http://www.coderli.com/archives/spring-filesystem-filepath-parse/" title="[原创] Spring源码学习-FileSystemXmlApplicationContext路径格式及解析方式"><span style="color: #0000ff;">《[原创] Spring源码学习-FileSystemXmlApplicationContext路径格式及解析方式》</span></a></span>的问题：<span style="color: #ff0000;">如果路径包含通配符(?,*，**)spring是怎么处理的？如果是以classpath*开头的又是如何呢？</span>
<div>
	先测试分析包含通配符(?)的。</div>
<div>
	<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;/**
	 * 测试包含通配符:*,?的路径
	 * &lt;p&gt;D:\\workspace-home\\spring-custom\\src\\main\\resources\\spring\\ap?-context.xml&lt;/p&gt;
	 * 通过读取配置文件失败的情况，因为此时Spring不支持\\路径的通配符解析
	 * 
	 * @author lihzh
	 * @date 2012-5-5 上午10:53:53
	 */
	@Test
	public void testAntStylePathFail() {
		String pathOne = &quot;D:\\workspace-home\\spring-custom\\src\\main\\resources\\spring\\ap?-context.xml&quot;;
		ApplicationContext appContext = new FileSystemXmlApplicationContext(pathOne);
		assertNotNull(appContext);
		VeryCommonBean bean = null;
		try {
			bean = appContext.getBean(VeryCommonBean.class);
			fail(&quot;Should not find the [VeryCommonBean].&quot;);
		} catch (NoSuchBeanDefinitionException e) {
		}
		assertNull(bean);
	}

</pre>
</div>
<div>
	<br />
	<span style="font-family: Tahoma; font-size: 14px; ">正如测试用例所写，实际是找不到该Bean的。这又是</span><font color="#ff0000" style="font-family: Tahoma; font-size: 14px; ">为什么</font><span style="font-family: Tahoma; font-size: 14px; ">？Spring不是支持通配符吗？FileSystemXmlApplicationContext的注释里也提到了通配符的情况：</span><br />
	<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
 * &lt;p&gt;The config location defaults can be overridden via {@link #getConfigLocations},
 * Config locations can either denote concrete files like &quot;/myfiles/context.xml&quot;
 * or Ant-style patterns like &quot;/myfiles/*-context.xml&quot; (see the
 * {@link org.springframework.util.AntPathMatcher} javadoc for pattern details).
</pre>
</div>
<div>
	从代码中寻找答案。回到上回的else分支中，因为包含通配符，所以进入第一个子分支。<br />
	<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
 /**
	 * Find all resources that match the given location pattern via the
	 * Ant-style PathMatcher. Supports resources in jar files and zip files
	 * and in the file system.
	 * @param locationPattern the location pattern to match
	 * @return the result as Resource array
	 * @throws IOException in case of I/O errors
	 * @see #doFindPathMatchingJarResources
	 * @see #doFindPathMatchingFileResources
	 * @see org.springframework.util.PathMatcher
	 */
	protected Resource[] findPathMatchingResources(String locationPattern) throws IOException {
		String rootDirPath = determineRootDir(locationPattern);
		String subPattern = locationPattern.substring(rootDirPath.length());
		Resource[] rootDirResources = getResources(rootDirPath);
		Set&lt;Resource&gt; result = new LinkedHashSet&lt;Resource&gt;(16);
		for (Resource rootDirResource : rootDirResources) {
			rootDirResource = resolveRootDirResource(rootDirResource);
			if (isJarResource(rootDirResource)) {
				result.addAll(doFindPathMatchingJarResources(rootDirResource, subPattern));
			}
			else if (rootDirResource.getURL().getProtocol().startsWith(ResourceUtils.URL_PROTOCOL_VFS)) {
				result.addAll(VfsResourceMatchingDelegate.findMatchingResources(rootDirResource, subPattern, getPathMatcher()));
			}
			else {
				result.addAll(doFindPathMatchingFileResources(rootDirResource, subPattern));
			}
		}
		if (logger.isDebugEnabled()) {
			logger.debug(&quot;Resolved location pattern [&quot; + locationPattern + &quot;] to resources &quot; + result);
		}
		return result.toArray(new Resource[result.size()]);
	}
</pre>
</div>
<div>
	&nbsp;</div>
<div>
	此方法传入的完整的没有处理的路径，从第一行开始，就开始分步处理解析传入的路径，首先是决定&ldquo;根&rdquo;路径: determineRootDir(locationPattern)</div>
<div>
	<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
    /**
	 * Determine the root directory for the given location.
	 * &lt;p&gt;Used for determining the starting point for file matching,
	 * resolving the root directory location to a &lt;code&gt;java.io.File&lt;/code&gt;
	 * and passing it into &lt;code&gt;retrieveMatchingFiles&lt;/code&gt;, with the
	 * remainder of the location as pattern.
	 * &lt;p&gt;Will return &quot;/WEB-INF/&quot; for the pattern &quot;/WEB-INF/*.xml&quot;,
	 * for example.
	 * @param location the location to check
	 * @return the part of the location that denotes the root directory
	 * @see #retrieveMatchingFiles
	 */
	protected String determineRootDir(String location) {
		int prefixEnd = location.indexOf(&quot;:&quot;) + 1;
		int rootDirEnd = location.length();
		while (rootDirEnd &gt; prefixEnd &amp;&amp; getPathMatcher().isPattern(location.substring(prefixEnd, rootDirEnd))) {
			rootDirEnd = location.lastIndexOf(&#39;/&#39;, rootDirEnd - 2) + 1;
		}
		if (rootDirEnd == 0) {
			rootDirEnd = prefixEnd;
		}
		return location.substring(0, rootDirEnd);
	}
</pre>
</div>
<div>
	这个&ldquo;根&rdquo;，就是不包含通配符的最长的部分，以我们的路径为例，这个&ldquo;根&rdquo;<span style="color: #ff0000;">本来应该是</span>： D:\\workspace-home\\spring-custom\\src\\main\\resources\\spring\\&nbsp;，但是实际上，从determineRootDir的实现可以看出，
	<div>
		<div>
			首先，先找到冒号：索引位，赋值给 prefixEnd&nbsp;。</div>
		<div>
			&nbsp;</div>
		<div>
			然后，在从冒号开始到最后的字符串中，循环判断是否包含通配符，如果包含，则截断最后一个由&quot;/&quot;分割的部分，例如：在我们路径中，就是最后的ap?-context.xml这一段。再循环判断剩下的部分，直到剩下的路径中都不包含通配符。</div>
		<div>
			如果查找完成后，rootDirEnd=0了，则将之前赋值的prefixEnd的值赋给rootDirEnd，也就是&quot;:&quot;所在的索引位。</div>
		<div>
			&nbsp;</div>
		<div>
			最后，将字符串从开始截断rootDirEnd。</div>
		<div>
			&nbsp;</div>
		<div>
			我们的问题，就出在关键的第二步，Spring这里只在字符串中查找&quot;/&quot;，并没有支持&quot;\\&quot;这样的路径分割方式，所以，自然找不到&quot;\\&quot;，rootDirEnd = -1 + 1 = 0。所以循环后，阶段出来的路径就是<span style="color: #ff0000;">D:</span>&nbsp;，自然Spring会找不到配置文件，容器无法初始化。</div>
		<div>
			&nbsp;</div>
		<div>
			基于以上分析，我们将路径修改为：D:/workspace-home/spring-custom/src/main/resources/spring/ap?-context.xml，再测试如下：</div>
		<div>
			<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
    /**
	 * 测试包含通配符:*,?的路径
	 * &lt;p&gt;D:/workspace-home/spring-custom/src/main/resources/spring/ap?-context.xml&lt;/p&gt;
	 * 通过读取配置文件
	 * 
	 * @author lihzh
	 * @date 2012-5-5 上午10:53:53
	 */
	@Test
	public void testAntStylePath() {
		String pathOne = &quot;D:/workspace-home/spring-custom/src/main/resources/spring/ap?-context.xml&quot;;
		ApplicationContext appContext = new FileSystemXmlApplicationContext(pathOne);
		assertNotNull(appContext);
		VeryCommonBean bean = appContext.getBean(VeryCommonBean.class);
		assertNotNull(bean);
		assertEquals(&quot;verycommonbean-name&quot;, bean.getName());
	}
</pre>
		</div>
	</div>
</div>
<div>
	<div>
		测试通过。</div>
	<div>
		&nbsp;</div>
	<div>
		刚才仅仅分析了，我们之前路径的问题所在，还有一点我想也是大家关心的，就是通配符是怎么匹配的呢？那我们就继续分析源码，回到 findPathMatchingResources&nbsp;方法。</div>
	<div>
		&nbsp;</div>
	<div>
		将路径分成包含通配符和不包含的两部分后，Spring会将根路径生成一个Resource，用的还是getResources方法。然后检查根路径的类型，是否是Jar路径？是否是VFS路径？对于我们这种普通路径，自然走到最后的分支。</div>
	<div>
		<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
    &nbsp;&nbsp; /**
	 * Find all resources in the file system that match the given location pattern
	 * via the Ant-style PathMatcher.
	 * @param rootDirResource the root directory as Resource
	 * @param subPattern the sub pattern to match (below the root directory)
	 * @return the Set of matching Resource instances
	 * @throws IOException in case of I/O errors
	 * @see #retrieveMatchingFiles
	 * @see org.springframework.util.PathMatcher
	 */
	protected Set&lt;Resource&gt; doFindPathMatchingFileResources(Resource rootDirResource, String subPattern)
			throws IOException {

		File rootDir;
		try {
			rootDir = rootDirResource.getFile().getAbsoluteFile();
		}
		catch (IOException ex) {
			if (logger.isWarnEnabled()) {
				logger.warn(&quot;Cannot search for matching files underneath &quot; + rootDirResource +
						&quot; because it does not correspond to a directory in the file system&quot;, ex);
			}
			return Collections.emptySet();
		}
		return doFindMatchingFileSystemResources(rootDir, subPattern);
	}
</pre>
	</div>
</div>
<div>
	<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
       /**
	 * Find all resources in the file system that match the given location pattern
	 * via the Ant-style PathMatcher.
	 * @param rootDir the root directory in the file system
	 * @param subPattern the sub pattern to match (below the root directory)
	 * @return the Set of matching Resource instances
	 * @throws IOException in case of I/O errors
	 * @see #retrieveMatchingFiles
	 * @see org.springframework.util.PathMatcher
	 */
	protected Set&lt;Resource&gt; doFindMatchingFileSystemResources(File rootDir, String subPattern) throws IOException {
		if (logger.isDebugEnabled()) {
			logger.debug(&quot;Looking for matching resources in directory tree [&quot; + rootDir.getPath() + &quot;]&quot;);
		}
		Set&lt;File&gt; matchingFiles = retrieveMatchingFiles(rootDir, subPattern);
		Set&lt;Resource&gt; result = new LinkedHashSet&lt;Resource&gt;(matchingFiles.size());
		for (File file : matchingFiles) {
			result.add(new FileSystemResource(file));
		}
		return result;
	}
</pre>
</div>
<div>
	<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
  &nbsp;&nbsp;&nbsp;&nbsp; /**
	 * Retrieve files that match the given path pattern,
	 * checking the given directory and its subdirectories.
	 * @param rootDir the directory to start from
	 * @param pattern the pattern to match against,
	 * relative to the root directory
	 * @return the Set of matching File instances
	 * @throws IOException if directory contents could not be retrieved
	 */
	protected Set&lt;File&gt; retrieveMatchingFiles(File rootDir, String pattern) throws IOException {
		if (!rootDir.exists()) {
			// Silently skip non-existing directories.
			if (logger.isDebugEnabled()) {
				logger.debug(&quot;Skipping [&quot; + rootDir.getAbsolutePath() + &quot;] because it does not exist&quot;);
			}
			return Collections.emptySet();
		}
		if (!rootDir.isDirectory()) {
			// Complain louder if it exists but is no directory.
			if (logger.isWarnEnabled()) {
				logger.warn(&quot;Skipping [&quot; + rootDir.getAbsolutePath() + &quot;] because it does not denote a directory&quot;);
			}
			return Collections.emptySet();
		}
		if (!rootDir.canRead()) {
			if (logger.isWarnEnabled()) {
				logger.warn(&quot;Cannot search for matching files underneath directory [&quot; + rootDir.getAbsolutePath() +
						&quot;] because the application is not allowed to read the directory&quot;);
			}
			return Collections.emptySet();
		}
		String fullPattern = StringUtils.replace(rootDir.getAbsolutePath(), File.separator, &quot;/&quot;);
		if (!pattern.startsWith(&quot;/&quot;)) {
			fullPattern += &quot;/&quot;;
		}
		fullPattern = fullPattern + StringUtils.replace(pattern, File.separator, &quot;/&quot;);
		Set&lt;File&gt; result = new LinkedHashSet&lt;File&gt;(8);
		doRetrieveMatchingFiles(fullPattern, rootDir, result);
		return result;
	}

</pre>
</div>
<div>
	<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
        /**
	 * Recursively retrieve files that match the given pattern,
	 * adding them to the given result list.
	 * @param fullPattern the pattern to match against,
	 * with prepended root directory path
	 * @param dir the current directory
	 * @param result the Set of matching File instances to add to
	 * @throws IOException if directory contents could not be retrieved
	 */
	protected void doRetrieveMatchingFiles(String fullPattern, File dir, Set&lt;File&gt; result) throws IOException {
		if (logger.isDebugEnabled()) {
			logger.debug(&quot;Searching directory [&quot; + dir.getAbsolutePath() +
					&quot;] for files matching pattern [&quot; + fullPattern + &quot;]&quot;);
		}
		File[] dirContents = dir.listFiles();
		if (dirContents == null) {
			if (logger.isWarnEnabled()) {
				logger.warn(&quot;Could not retrieve contents of directory [&quot; + dir.getAbsolutePath() + &quot;]&quot;);
			}
			return;
		}
		for (File content : dirContents) {
			String currPath = StringUtils.replace(content.getAbsolutePath(), File.separator, &quot;/&quot;);
			if (content.isDirectory() &amp;&amp; getPathMatcher().matchStart(fullPattern, currPath + &quot;/&quot;)) {
				if (!content.canRead()) {
					if (logger.isDebugEnabled()) {
						logger.debug(&quot;Skipping subdirectory [&quot; + dir.getAbsolutePath() +
								&quot;] because the application is not allowed to read the directory&quot;);
					}
				}
				else {
					doRetrieveMatchingFiles(fullPattern, content, result);
				}
			}
			if (getPathMatcher().match(fullPattern, currPath)) {
				result.add(content);
			}
		}
	}
</pre>
</div>
<div>
	<div>
		主要的匹配工作，是从<span style="color: #ff0000;">doRetrieveMatchingFiles</span>&nbsp;方法开始的。前面的都是简单的封装过渡，在retrieveMatchingFiles中判断了下根路径是否存在、是否是文件夹、是否可读。否则都直接返回空集合。都满足了以后才进入，<span style="color: #ff0000;">doRetrieveMatchingFiles</span>&nbsp;方法。在该方法中，</div>
	<div>
		&nbsp;</div>
	<div>
		首先，列出该文件夹下的所有文件。</div>
	<div>
		然后，遍历所有文件，如果仍是文件夹，递归调用doRetrieveMatchingFiles方法。如果不是，则调用getPathMatcher().match(fullPattern, currPath)进行文件名的最后匹配，将满足条件放入结果集。</div>
	<div>
		&nbsp;</div>
	<div>
		该match方法，实际是调用了AntPathMatcher的doMatch方法，</div>
	<div>
		<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
        /**
	 * Actually match the given &lt;code&gt;path&lt;/code&gt; against the given &lt;code&gt;pattern&lt;/code&gt;.
	 * @param pattern the pattern to match against
	 * @param path the path String to test
	 * @param fullMatch whether a full pattern match is required (else a pattern match
	 * as far as the given base path goes is sufficient)
	 * @return &lt;code&gt;true&lt;/code&gt; if the supplied &lt;code&gt;path&lt;/code&gt; matched, &lt;code&gt;false&lt;/code&gt; if it didn&#39;t
	 */
	protected boolean doMatch(String pattern, String path, boolean fullMatch,
			Map&lt;String, String&gt; uriTemplateVariables) {

		if (path.startsWith(this.pathSeparator) != pattern.startsWith(this.pathSeparator)) {
			return false;
		}

		String[] pattDirs = StringUtils.tokenizeToStringArray(pattern, this.pathSeparator);
		String[] pathDirs = StringUtils.tokenizeToStringArray(path, this.pathSeparator);

		int pattIdxStart = 0;
		int pattIdxEnd = pattDirs.length - 1;
		int pathIdxStart = 0;
		int pathIdxEnd = pathDirs.length - 1;

		// Match all elements up to the first **
		while (pattIdxStart &lt;= pattIdxEnd &amp;&amp; pathIdxStart &lt;= pathIdxEnd) {
			String patDir = pattDirs[pattIdxStart];
			if (&quot;**&quot;.equals(patDir)) {
				break;
			}
			if (!matchStrings(patDir, pathDirs[pathIdxStart], uriTemplateVariables)) {
				return false;
			}
			pattIdxStart++;
			pathIdxStart++;
		}

		if (pathIdxStart &gt; pathIdxEnd) {
			// Path is exhausted, only match if rest of pattern is * or **&#39;s
			if (pattIdxStart &gt; pattIdxEnd) {
				return (pattern.endsWith(this.pathSeparator) ? path.endsWith(this.pathSeparator) :
						!path.endsWith(this.pathSeparator));
			}
			if (!fullMatch) {
				return true;
			}
			if (pattIdxStart == pattIdxEnd &amp;&amp; pattDirs[pattIdxStart].equals(&quot;*&quot;) &amp;&amp; path.endsWith(this.pathSeparator)) {
				return true;
			}
			for (int i = pattIdxStart; i &lt;= pattIdxEnd; i++) {
				if (!pattDirs[i].equals(&quot;**&quot;)) {
					return false;
				}
			}
			return true;
		}
		else if (pattIdxStart &gt; pattIdxEnd) {
			// String not exhausted, but pattern is. Failure.
			return false;
		}
		else if (!fullMatch &amp;&amp; &quot;**&quot;.equals(pattDirs[pattIdxStart])) {
			// Path start definitely matches due to &quot;**&quot; part in pattern.
			return true;
		}

		// up to last &#39;**&#39;
		while (pattIdxStart &lt;= pattIdxEnd &amp;&amp; pathIdxStart &lt;= pathIdxEnd) {
			String patDir = pattDirs[pattIdxEnd];
			if (patDir.equals(&quot;**&quot;)) {
				break;
			}
			if (!matchStrings(patDir, pathDirs[pathIdxEnd], uriTemplateVariables)) {
				return false;
			}
			pattIdxEnd--;
			pathIdxEnd--;
		}
		if (pathIdxStart &gt; pathIdxEnd) {
			// String is exhausted
			for (int i = pattIdxStart; i &lt;= pattIdxEnd; i++) {
				if (!pattDirs[i].equals(&quot;**&quot;)) {
					return false;
				}
			}
			return true;
		}

		while (pattIdxStart != pattIdxEnd &amp;&amp; pathIdxStart &lt;= pathIdxEnd) {
			int patIdxTmp = -1;
			for (int i = pattIdxStart + 1; i &lt;= pattIdxEnd; i++) {
				if (pattDirs[i].equals(&quot;**&quot;)) {
					patIdxTmp = i;
					break;
				}
			}
			if (patIdxTmp == pattIdxStart + 1) {
				// &#39;**/**&#39; situation, so skip one
				pattIdxStart++;
				continue;
			}
			// Find the pattern between padIdxStart &amp; padIdxTmp in str between
			// strIdxStart &amp; strIdxEnd
			int patLength = (patIdxTmp - pattIdxStart - 1);
			int strLength = (pathIdxEnd - pathIdxStart + 1);
			int foundIdx = -1;

			strLoop:
			for (int i = 0; i &lt;= strLength - patLength; i++) {
				for (int j = 0; j &lt; patLength; j++) {
					String subPat = pattDirs[pattIdxStart + j + 1];
					String subStr = pathDirs[pathIdxStart + i + j];
					if (!matchStrings(subPat, subStr, uriTemplateVariables)) {
						continue strLoop;
					}
				}
				foundIdx = pathIdxStart + i;
				break;
			}

			if (foundIdx == -1) {
				return false;
			}

			pattIdxStart = patIdxTmp;
			pathIdxStart = foundIdx + patLength;
		}

		for (int i = pattIdxStart; i &lt;= pattIdxEnd; i++) {
			if (!pattDirs[i].equals(&quot;**&quot;)) {
				return false;
			}
		}

		return true;
	}
</pre>
	</div>
</div>
<div>
	<div>
		比较方法如下，</div>
	<div>
		&nbsp;</div>
	<div>
		首先，分别将输入路径和待比较路径，按照文件分隔符分割成字符串数组。（例如：｛&rdquo;D:&ldquo;, &quot;workspace-home&quot;, &quot;spring-custom&quot;...｝）</div>
	<div>
		然后，设置好起始和结束位后，对这两个数组进行while循环（代码中第一个while循环），逐断比较匹配(matchStrings)情况。如果有一段不满足则返回fasle。</div>
	<div>
		&nbsp;</div>
	<div>
		<div>
			由于我们当前的测试路径中不包含**的部分，所以主要的判断基本都在第一个while就可以搞定。这部分工作自然是由matchStrings完成的。</div>
		<div>
			&nbsp;</div>
		<div>
			<span style="color: #ff0000;">试想一下：</span>如果让你完成一个通配符路径匹配的功能，你会如何去做？是否自然的联想到了正则？似乎是个好选择，看看spring是怎么做的。</div>
		<div>
			<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
private boolean matchStrings(String pattern, String str, Map&lt;String, String&gt; uriTemplateVariables) {
		AntPathStringMatcher matcher = new AntPathStringMatcher(pattern, str, uriTemplateVariables);
		return matcher.matchStrings();
	}
</pre>
		</div>
	</div>
	<div>
		在构造AntPathStringMatcher实例的时候，spring果然也创建了正则：</div>
	<div>
		<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
AntPathStringMatcher(String pattern, String str, Map&lt;String, String&gt; uriTemplateVariables) {
		this.str = str;
		this.uriTemplateVariables = uriTemplateVariables;
		this.pattern = createPattern(pattern);
	}

private Pattern createPattern(String pattern) {
		StringBuilder patternBuilder = new StringBuilder();
		Matcher m = GLOB_PATTERN.matcher(pattern);
		int end = 0;
		while (m.find()) {
			patternBuilder.append(quote(pattern, end, m.start()));
			String match = m.group();
			if (&quot;?&quot;.equals(match)) {
				patternBuilder.append(&#39;.&#39;);
			}
			else if (&quot;*&quot;.equals(match)) {
				patternBuilder.append(&quot;.*&quot;);
			}
			else if (match.startsWith(&quot;{&quot;) &amp;&amp; match.endsWith(&quot;}&quot;)) {
				int colonIdx = match.indexOf(&#39;:&#39;);
				if (colonIdx == -1) {
					patternBuilder.append(DEFAULT_VARIABLE_PATTERN);
					variableNames.add(m.group(1));
				}
				else {
					String variablePattern = match.substring(colonIdx + 1, match.length() - 1);
					patternBuilder.append(&#39;(&#39;);
					patternBuilder.append(variablePattern);
					patternBuilder.append(&#39;)&#39;);
					String variableName = match.substring(1, colonIdx);
					variableNames.add(variableName);
				}
			}
			end = m.end();
		}
		patternBuilder.append(quote(pattern, end, pattern.length()));
		return Pattern.compile(patternBuilder.toString());
	}
</pre>
	</div>
</div>
<div>
	简单说，就是spring先用正则：</div>
<div>
	<span style="color: #ff0000;">private static final Pattern GLOB_PATTERN = Pattern.compile(&quot;\\?|\\*|\\{((?:\\{[^/]+?\\}|[^/{}]|\\\\[{}])+?)\\}&quot;);</span></div>
<div>
	&nbsp;</div>
<div>
	找到路径中的&quot;?&quot;和&quot;*&quot;通配符，然后转换为Java正则的任意字符&quot;.&quot;和&quot;.*&quot;。生成另一个正则表达式去匹配查找到的文件的路径。如果匹配则返回true。
	<div>
		&nbsp;</div>
	至此，对于路径中包含?和*的情况解析spring的解析方式，我们已经基本了解了。本来想把**的情况一起介绍了，不过考虑的篇幅过长，我们下次再一起研究吧。
	<div>
		&nbsp;</div>
	<div>
		写在最后：所有研究均为笔者工作之余消遣之做，错误指出还望指出，欢迎各种形势的探讨。</div>
</div>

