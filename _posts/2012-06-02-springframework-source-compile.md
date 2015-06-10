---
layout: post
title: SpringFramework3 源码下载和编译教程
date: 2012-06-02 12:42
author: onecoder
comments: true
categories: [Spring, Spring, 源码, 编译]
---
Spring已经将源码从svn迁移到了git。所以，首先需要安装git环境。我使用的是Windows下的TortoiseGit，带图形界面，比较直观。<br />
SpringFramework的GitHub地址： <span style="color: #0000ff;"><a href="https://github.com/SpringSource/spring-framework" target="\&quot;_blank\&quot;"><span style="color: #0000ff;">https://github.com/SpringSource/spring-framework</span></a></span><br />
Git clone地址： <span style="color: #0000ff;"><a href="git://github.com/SpringSource/spring-framework.git" target="\&quot;_blank\&quot;" title="git://github.com/SpringSource/spring-framework.git"><span style="color: #0000ff;">git://github.com/SpringSource/spring-framework.git</span></a></span><br />
下载后，进入下载到的文件夹，里面有个README.md的说明文件，如果E文可以，可以按照上面的说明去做。
<div style="text-align: center; ">
	<a href="http://www.coderli.com/wp-content/uploads/2012/06/1.jpeg"><img alt="spring源码目录" class="alignnone size-full wp-image-239" height="190" src="http://www.coderli.com/wp-content/uploads/2012/06/1.jpeg" title="spring" width="191" /></a></div>
接下来编译工程 Spring采用的是Gradle进行项目管理，<br />
官网地址： <a href="http://gradle.org/overview" target="\&quot;_blank\&quot;">http://gradle.org/overview</a>
<div style="text-align: center; ">
	通过命令行进入刚在下载的目录（双击gradlew.bat貌似也可以），运行： ./gradlew build 开始下载gradle程序和依赖的jar文件等。这个过程比较耗时。等待&hellip;&hellip; 半个小时之后，居然&hellip;&hellip;failed！ 查看原因原来是测试代码failed，于是重新执行命令 ./gradlew build &ndash;x test 忽略测试代码，终于编译成功 <a href="http://www.coderli.com/wp-content/uploads/2012/06/2.jpeg"><img alt="命令行" class="alignnone size-full wp-image-240" height="248" src="http://www.coderli.com/wp-content/uploads/2012/06/2.jpeg" title="Spring命令行" width="474" /></a></div>
<div>
	执行 ./gradlew install 将jar包安装到gradle库 接下来是将源码导入到IDE中。 由于我是windows用户，所以参考import-into-idea.md的说明 这里，由于我使用的是eclipse，所以执行： ./gradlew cleanIdea eclipse 生成Eclipse导入需要的文件 剩下的就是导入文件了：</div>
<div style="text-align: center; ">
	<a href="http://www.coderli.com/wp-content/uploads/2012/06/3.jpeg"><img alt="Spring导入Eclipse" class="alignnone size-full wp-image-241" height="270" src="http://www.coderli.com/wp-content/uploads/2012/06/3.jpeg" title="导入Eclipse" width="287" /></a></div>
<div style="text-align: left; ">
	PS1：spring-oxm工程的有几个测似用例有编译错误，找不到他引用的类，之前下载的代码也有此问题，不知道是我下载的问题，还是spring那面的疏忽，不过只是测试用例，你可以自己修改一下，或者干脆删除就好了。 PS2：图中的MySpring是我自己创建的分支。</div>

