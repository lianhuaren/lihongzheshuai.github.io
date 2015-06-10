---
layout: post
title: Maven库中.lastUpdated文件自动清除工具
date: 2012-06-26 21:43
author: onecoder
comments: true
categories: [lastupdated, Maven, Maven, 删除, 开发环境, 批量]
---
<p style="padding: 0px 0px 15px; margin: 0px; color: rgb(44, 44, 44); font-family: 宋体, 'Arial Narrow', arial, serif; font-size: 14px; line-height: 28px;">
	<span style="padding: 0px; margin: 0px; font-family: Helvetica, Tahoma, Arial, sans-serif; line-height: 25px; ">最近开发过程中，在更新maven库时，如果网络问不定或者是一些自己手动安装到本地maven库的jar包，在中心库找不到对应的jar，会生成一些.lastUpdated文件，会导致m2e工具无法找到依赖的jar包，从而提示编译错误。</span></p>
<p style="padding: 0px; margin: 0px; color: rgb(44, 44, 44); font-size: 14px; font-family: Helvetica, Tahoma, Arial, sans-serif; line-height: 25px; ">
	对于该问题，我也没有找到很好的解决方案，只能手动删除一下lastUpdated文件。文件多时十分繁琐。网上看到别人的解决方案也有利用命令行命令，匹配文件扩展名批量删除的。命令行不会，于是就写了几行代码用于删除.lastUpdated文件。</p>
<p style="padding: 0px; margin: 0px; color: rgb(44, 44, 44); font-size: 14px; font-family: Helvetica, Tahoma, Arial, sans-serif; line-height: 25px; ">
	<span style="padding: 0px; margin: 0px; color: rgb(255, 0, 0); "><strong style="padding: 0px; margin: 0px; ">如有其他直接的解决方案，望不吝赐教，写代码实属无奈之举。</strong></span></p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
public class DelLastUpdated { 
 
    private static PropertyHelper propHelper = new PropertyHelper(&quot;config&quot;); 
    private static final String KEY_MAVEN_REPO = &quot;maven.repo&quot;; 
    private static final String MAVEN_REPO_PATH = propHelper 
            .getValue(KEY_MAVEN_REPO); 
    private static final String FILE_SUFFIX = &quot;lastUpdated&quot;; 
    private static final Log _log = LogFactory.getLog(DelLastUpdated.class); 
 
    /** 
     * @param args 
     */ 
    public static void main(String[] args) { 
        File mavenRep = new File(MAVEN_REPO_PATH); 
        if (!mavenRep.exists()) { 
            _log.warn(&quot;Maven repos is not exist.&quot;); 
            return; 
        } 
        File[] files = mavenRep.listFiles((FilenameFilter) FileFilterUtils 
                .directoryFileFilter()); 
        delFileRecr(files,null); 
        _log.info(&quot;Clean lastUpdated files finished.&quot;); 
    } 
 
    private static void delFileRecr(File[] dirs, File[] files) { 
        if (dirs != null &amp;&amp; dirs.length &gt; 0) { 
            for(File dir: dirs){ 
                File[] childDir = dir.listFiles((FilenameFilter) FileFilterUtils 
                .directoryFileFilter()); 
                File[] childFiles = dir.listFiles((FilenameFilter) FileFilterUtils 
                .suffixFileFilter(FILE_SUFFIX)); 
                delFileRecr(childDir,childFiles); 
            } 
        } 
        if(files!=null&amp;&amp;files.length&gt;0){ 
            for(File file: files){ 
                if(file.delete()){ 
                    _log.info(&quot;File: [&quot;+file.getName()+&quot;] has been deleted.&quot;); 
                } 
            } 
        } 
    } 
 
} 
</pre>
<br />
<span style="color: rgb(44, 44, 44); font-family: Helvetica, Tahoma, Arial, sans-serif; font-size: 14px; line-height: 25px; ">配置文件：config.properties</span><br />
<pre class="brush:xml;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
maven.repo=D:\\.m2\\repository 
</pre>
<p style="padding: 0px 0px 15px; margin: 0px; color: rgb(44, 44, 44); font-family: 宋体, 'Arial Narrow', arial, serif; font-size: 14px; line-height: 28px; ">
	<span style="padding: 0px; margin: 0px; font-family: Helvetica, Tahoma, Arial, sans-serif; line-height: 25px; ">源码下载地址：</span></p>
<p style="padding: 0px 0px 15px; margin: 0px; color: rgb(44, 44, 44); font-family: 宋体, 'Arial Narrow', arial, serif; font-size: 14px; line-height: 28px; ">
	&nbsp;svn:&nbsp;https://svn.code.sf.net/p/maventools/code/trunk/maven-tools</p>
<p style="padding: 0px 0px 15px; margin: 0px; color: rgb(44, 44, 44); font-family: 宋体, 'Arial Narrow', arial, serif; font-size: 14px; line-height: 28px; ">
	<span style="padding: 0px; margin: 0px; font-family: Helvetica, Tahoma, Arial, sans-serif; line-height: 25px; ">工程里还包括一个批量安装jar包到本地maven库的工具。以后再另外介绍。</span></p>

