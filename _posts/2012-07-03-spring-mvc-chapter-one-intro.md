---
layout: post
title: SpringMVC - 第一章 Web MVC简介
date: 2012-07-03 22:12
author: onecoder
comments: true
categories: [mvc, Spring, springmvc]
---
<h2 style="padding: 0px; margin: 0px 0px 0.5em; line-height: 1.5em; font-family: verdana, arial, helvetica, sans-serif; ">
	Web MVC简介</h2>
<h3 style="padding: 0px; margin: 0px 0px 0.5em; font-size: 1.2em; line-height: 1.5em; font-family: verdana, arial, helvetica, sans-serif; ">
	1.1、Web开发中的请求-响应模型：</h3>
<p align="center" style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;<img alt="" src="http://sishuok.com/forum/upload/2012/7/1/2fc5edc55fa262fddccecc1816f5ff7b__1.JPG" style="padding: 0px; margin: 0px; border-width: 0px; border-style: none; max-width: 100%; height: auto !important; line-height: 1.5em; " /></p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	在Web世界里，具体步骤如下：</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	1、&nbsp; Web浏览器（如IE）发起请求，如访问<a href="http://sishuok.com/" style="padding: 0px; margin: 0px; color: rgb(0, 94, 167); text-decoration: none; line-height: 1.5em; ">http://sishuok.com</a></p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	2、&nbsp; Web服务器（如Tomcat）接收请求，处理请求（比如用户新增，则将把用户保存一下），最后产生响应（一般为html）。</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	3、web服务器处理完成后，返回内容给web客户端（一般就是我们的浏览器），客户端对接收的内容进行处理（如web浏览器将会对接收到的html内容进行渲染以展示给客户）。</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	<strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">因此，在Web</strong><strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">世界里：</strong></p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	都是Web客户端发起请求，Web服务器接收、处理并产生响应。</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	一般Web服务器是不能主动通知Web客户端更新内容。虽然现在有些技术如服务器推（如Comet）、还有现在的HTML5 websocket可以实现Web服务器主动通知Web客户端。</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	到此我们了解了在web开发时的请求/响应模型，接下来我们看一下标准的MVC模型是什么。</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;</p>
<h3 style="padding: 0px; margin: 0px 0px 0.5em; font-size: 1.2em; line-height: 1.5em; font-family: verdana, arial, helvetica, sans-serif; ">
	1.2、标准MVC模型概述</h3>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	<strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">MVC</strong><strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">模型：</strong>是一种架构型的模式，本身不引入新功能，只是帮助我们将开发的结构组织的更加合理，使展示与模型分离、流程控制逻辑、业务逻辑调用与展示逻辑分离。如图1-2</p>
<p align="center" style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;<img alt="" src="http://sishuok.com/forum/upload/2012/7/1/a633b31a42f0224c9bf66cd3cc886e04__2.JPG" style="padding: 0px; margin: 0px; border-width: 0px; border-style: none; max-width: 100%; height: auto !important; line-height: 1.5em; " /></p>
<p align="center" style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	图1-2</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	<strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">首先让我们了解下MVC</strong><strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">（Model-View-Controller</strong><strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">）三元组的概念：</strong></p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	<strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">Model</strong><strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">（模型）：</strong>数据模型，提供要展示的数据，因此包含数据和行为，可以认为是领域模型或JavaBean组件（包含数据和行为），不过现在一般都分离开来：Value Object（数据） 和 服务层（行为）。也就是模型提供了模型数据查询和模型数据的状态更新等功能，包括数据和业务。</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	<strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">View</strong><strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">（视图）：</strong>负责进行模型的展示，一般就是我们见到的用户界面，客户想看到的东西。</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	<strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">&nbsp;</strong></p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	<strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">Controller</strong><strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">（控制器）：</strong>接收用户请求，委托给模型进行处理（状态改变），处理完毕后把返回的模型数据返回给视图，由视图负责展示。 也就是说控制器做了个调度员的工作，。</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	从图1-1我们还看到，在标准的MVC中模型能主动推数据给视图进行更新（观察者设计模式，在模型上注册视图，当模型更新时自动更新视图），但在Web开发中模型是无法主动推给视图（无法主动更新用户界面），因为在Web开发是请求-响应模型。</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	那接下来我们看一下在Web里MVC是什么样子，我们称其为 Web MVC 来区别标准的MVC。</p>
<h3 style="padding: 0px; margin: 0px 0px 0.5em; font-size: 1.2em; line-height: 1.5em; font-family: verdana, arial, helvetica, sans-serif; ">
	1.3、Web MVC概述</h3>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	模型-视图-控制器概念和标准MVC概念一样，请参考1.2，我们再看一下Web MVC标准架构，如图1-3：</p>
<p align="center" style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	<img alt="" src="http://sishuok.com/forum/upload/2012/7/1/baa1df353ed98b79231b535bc1f73dea__3.JPG" style="padding: 0px; margin: 0px; border-width: 0px; border-style: none; max-width: 100%; height: auto !important; line-height: 1.5em; " /></p>
<p align="center" style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	如图1-3</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	在Web MVC模式下，模型无法主动推数据给视图，如果用户想要视图更新，需要再发送一次请求（即请求-响应模型）。</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	概念差不多了，我们接下来了解下Web端开发的发展历程，和使用代码来演示一下Web MVC是如何实现的，还有为什么要使用MVC这个模式呢？</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;</p>
<h3 style="padding: 0px; margin: 0px 0px 0.5em; font-size: 1.2em; line-height: 1.5em; font-family: verdana, arial, helvetica, sans-serif; ">
	1.4、Web端开发发展历程</h3>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	此处我们只是简单的叙述比较核心的历程，如图1-4</p>
<p align="center" style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;<img alt="" src="http://sishuok.com/forum/upload/2012/7/1/41f193e4f961be27d511789df2ee7680__4.JPG" style="padding: 0px; margin: 0px; border-width: 0px; border-style: none; max-width: 100%; height: auto !important; line-height: 1.5em; " /></p>
<p align="center" style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	图1-4</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	<strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">1.4.1</strong><strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">、CGI</strong>：（Common Gateway Interface）公共网关接口，一种在web服务端使用的脚本技术，使用C或Perl语言编写，用于接收web用户请求并处理，最后动态产生响应给用户，但每次请求将产生一个进程，重量级。</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	<strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">&nbsp;</strong></p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	<strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">1.4.2</strong><strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">、Servlet</strong>：一种JavaEE web组件技术，是一种在服务器端执行的web组件，用于接收web用户请求并处理，最后动态产生响应给用户。但每次请求只产生一个线程（而且有线程池），轻量级。而且能利用许多JavaEE技术（如JDBC等）。本质就是在java代码里面 输出 html流。但表现逻辑、控制逻辑、业务逻辑调用混杂。如图1-5&nbsp;&nbsp;&nbsp;&nbsp;</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	<img alt="" src="http://sishuok.com/forum/upload/2012/7/1/799db5c79c85cb59b68b915916f8dddc__5.JPG" style="padding: 0px; margin: 0px; border-width: 0px; border-style: none; max-width: 100%; height: auto !important; line-height: 1.5em; " /></p>
<p align="center" style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	图1-5</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	如图1-5，这种做法是绝对不可取的，控制逻辑、表现代码、业务逻辑对象调用混杂在一起，最大的问题是直接在Java代码里面输出Html，这样前端开发人员无法进行页面风格等的设计与修改，即使修改也是很麻烦，因此实际项目这种做法不可取。</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	<strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">1.4.3</strong><strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">、JSP</strong><strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">：</strong>（Java Server Page）：一种在服务器端执行的web组件，是一种运行在标准的HTML页面中嵌入脚本语言（现在只支持Java）的模板页面技术。本质就是在html代码中嵌入java代码。JSP最终还是会被编译为Servlet，只不过比纯Servlet开发页面更简单、方便。但表现逻辑、控制逻辑、业务逻辑调用还是混杂。如图1-6</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;</p>
<p align="center" style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;<img alt="" src="http://sishuok.com/forum/upload/2012/7/1/7486df5510b6068a360d8f0c6bbb706c__6.JPG" style="padding: 0px; margin: 0px; border-width: 0px; border-style: none; max-width: 100%; height: auto !important; line-height: 1.5em; " /></p>
<p align="center" style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	图1-6</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	如图1-6，这种做法也是绝对不可取的，控制逻辑、表现代码、业务逻辑对象调用混杂在一起，但比直接在servlet里输出html要好一点，前端开发人员可以进行简单的页面风格等的设计与修改（但如果嵌入的java脚本太多也是很难修改的），因此实际项目这种做法不可取。</p>
<p align="center" style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;<img alt="" src="http://sishuok.com/forum/upload/2012/7/1/c59e61daa2ed98f75aab1ae3397fb235__7.JPG" style="padding: 0px; margin: 0px; border-width: 0px; border-style: none; max-width: 100%; height: auto !important; line-height: 1.5em; " /></p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	JSP本质还是Servlet，最终在运行时会生成一个Servlet（如tomcat，将在tomcat\work\Catalina\web应用名\org\apache\jsp下生成），但这种使得写html简单点，但仍是控制逻辑、表现代码、业务逻辑对象调用混杂在一起。</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	<strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">1.4.4</strong><strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">、Model1</strong><strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">：</strong>可以认为是JSP的增强版，可以认为是jsp+javabean如图1-7</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	特点：使用&lt;jsp:useBean&gt;标准动作，自动将请求参数封装为JavaBean组件；还必须使用java脚本执行控制逻辑。</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;</p>
<p align="center" style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;<img alt="" src="http://sishuok.com/forum/upload/2012/7/1/c25b9ce9064a37ab93ffe02711b3ecc7__8.JPG" style="padding: 0px; margin: 0px; border-width: 0px; border-style: none; max-width: 100%; height: auto !important; line-height: 1.5em; " /></p>
<p align="center" style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	图1-7</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	此处我们可以看出，使用&lt;jsp:useBean&gt;标准动作可以简化javabean的获取/创建，及将请求参数封装到javabean，再看一下Model1架构，如图1-8。</p>
<p align="center" style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;<img alt="" src="http://sishuok.com/forum/upload/2012/7/1/d3b5b2ec88706fff7ef4b98d110837d1__9.JPG" style="padding: 0px; margin: 0px; border-width: 0px; border-style: none; max-width: 100%; height: auto !important; line-height: 1.5em; " /></p>
<p align="center" style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	图1-8 Model1架构</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	Model1架构中，JSP负责控制逻辑、表现逻辑、业务对象（javabean）的调用，只是比纯JSP简化了获取请求参数和封装请求参数。同样是不好的，在项目中应该严禁使用（或最多再demo里使用）。</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	<strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">1.4.5</strong><strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">、Model2</strong><strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">：</strong>在JavaEE世界里，它可以认为就是<strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">Web MVC</strong>模型</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	Model2架构其实可以认为就是我们所说的Web MVC模型，只是控制器采用Servlet、模型采用JavaBean、视图采用JSP，如图1-9</p>
<p align="center" style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;<img alt="" src="http://sishuok.com/forum/upload/2012/7/1/a6b7b2ca293e610a7a2b32e47a16d718__10.JPG" style="padding: 0px; margin: 0px; border-width: 0px; border-style: none; max-width: 100%; height: auto !important; line-height: 1.5em; " /></p>
<p align="center" style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	图1-9 Model2架构</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	具体代码事例如下：</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;<img alt="" src="http://sishuok.com/forum/upload/2012/7/1/254a61cd5c20f1e8f0e1012f02cdaa31__11.JPG" style="padding: 0px; margin: 0px; border-width: 0px; border-style: none; max-width: 100%; height: auto !important; line-height: 1.5em; " /></p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;<img alt="" src="http://sishuok.com/forum/upload/2012/7/1/0ec4c098750790295eaad2f1b3a4dc82__12.JPG" style="padding: 0px; margin: 0px; border-width: 0px; border-style: none; max-width: 100%; height: auto !important; line-height: 1.5em; " /></p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	<img alt="" src="http://sishuok.com/forum/upload/2012/7/1/1f330763be94e5abe7ecdd7928930dbb__13.JPG" style="padding: 0px; margin: 0px; border-width: 0px; border-style: none; max-width: 100%; height: auto !important; line-height: 1.5em; " /></p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	从Model2架构可以看出，视图和模型分离了，控制逻辑和展示逻辑分离了。</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	但我们也看到严重的缺点：</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	1．&nbsp; 1、控制器：</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	1．1．1、控制逻辑可能比较复杂，其实我们可以按照规约，如请求参数submitFlag=toAdd，我们其实可以直接调用toAdd方法，来简化控制逻辑；而且每个模块基本需要一个控制器，造成控制逻辑可能很复杂；</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	1．1．2、请求参数到模型的封装比较麻烦，如果能交给框架来做这件事情，我们可以从中得到解放；</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	1．1．3、选择下一个视图，严重依赖Servlet API，这样很难或基本不可能更换视图；</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	1．1．4、给视图传输要展示的模型数据，使用Servlet API，更换视图技术也要一起更换，很麻烦。</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	1.2、模型：</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	1．2．1、此处模型使用JavaBean，可能造成JavaBean组件类很庞大，一般现在项目都是采用三层架构，而不采用JavaBean。</p>
<p align="center" style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;<img alt="" src="http://sishuok.com/forum/upload/2012/7/1/1570f54f6a52301d8ea58fe8fa9efb29__14.JPG" style="padding: 0px; margin: 0px; border-width: 0px; border-style: none; max-width: 100%; height: auto !important; line-height: 1.5em; " /></p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	1.3、视图</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	1．3．1、现在被绑定在JSP，很难更换视图，比如Velocity、FreeMarker；比如我要支持Excel、PDF视图等等。</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	<strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">1.4.5</strong><strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">、服务到工作者：Front Controller + Application Controller + Page Controller + Context</strong></p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	即，前端控制器+应用控制器+页面控制器（也有称其为动作）+上下文，也是Web MVC，只是责任更加明确，详情请参考《核心J2EE设计模式》和《企业应用架构模式》如图1-10：</p>
<p align="center" style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;<img alt="" src="http://sishuok.com/forum/upload/2012/7/1/e7fae17e52bb3664d0d3f4ea8db7ae55__15.JPG" style="padding: 0px; margin: 0px; border-width: 0px; border-style: none; max-width: 100%; height: auto !important; line-height: 1.5em; " /></p>
<p align="center" style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	图1-10</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	运行流程如下：</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;<img alt="" src="http://sishuok.com/forum/upload/2012/7/1/925c5ff2cc8613c898b05a4817db1f56__16.JPG" style="padding: 0px; margin: 0px; border-width: 0px; border-style: none; max-width: 100%; height: auto !important; line-height: 1.5em; " /></p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	职责：</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	<strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">Front Controller</strong><strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">：</strong>前端控制器，负责为表现层提供统一访问点，从而避免Model2中出现的重复的控制逻辑（由前端控制器统一回调相应的功能方法，如前边的根据submitFlag=login转调login方法）；并且可以为多个请求提供共用的逻辑（如准备上下文等等），将选择具体视图和具体的功能处理（如login里边封装请求参数到模型，并调用业务逻辑对象）分离。</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	<strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">&nbsp;</strong></p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	<strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">Application Controller</strong><strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">：</strong>应用控制器，前端控制器分离选择具体视图和具体的功能处理之后，需要有人来管理，应用控制器就是用来选择具体视图技术（视图的管理）和具体的功能处理（页面控制器/命令对象/动作管理），一种策略设计模式的应用，可以很容易的切换视图/页面控制器，相互不产生影响。</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	<strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">&nbsp;</strong></p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	<strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">Page Controller(Command)</strong><strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">：</strong>页面控制器/动作/处理器：功能处理代码，收集参数、封装参数到模型，转调业务对象处理模型，返回逻辑视图名交给前端控制器（和具体的视图技术解耦），由前端控制器委托给应用控制器选择具体的视图来展示，可以是命令设计模式的实现。页面控制器也被称为处理器或动作。</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	<strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">&nbsp;</strong></p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	<strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">Context</strong><strong style="padding: 0px; margin: 0px; line-height: 1.5em; ">：</strong>上下文，还记得Model2中为视图准备要展示的模型数据吗，我们直接放在request中（Servlet API相关），有了上下文之后，我们就可以将相关数据放置在上下文，从而与协议无关（如Servlet API）的访问/设置模型数据，一般通过ThreadLocal模式实现。</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	到此，我们回顾了整个web开发架构的发展历程，可能不同的web层框架在细节处理方面不同，但的目的是一样的：</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	干净的web表现层：</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;&nbsp;&nbsp; 模型和视图的分离；</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	控制器中的控制逻辑与功能处理分离（收集并封装参数到模型对象、业务对象调用）；</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	控制器中的视图选择与具体视图技术分离。</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	轻薄的web表现层：</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;&nbsp;&nbsp; 做的事情越少越好，薄薄的，不应该包含无关代码；</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 只负责收集并组织参数到模型对象，启动业务对象的调用；</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 控制器只返回逻辑视图名并由相应的应用控制器来选择具体使用的视图策略；</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 尽量少使用框架特定API，保证容易测试。</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	到此我们了解Web MVC的发展历程，接下来让我们了解下Spring MVC到底是什么、架构及来个HelloWorld了解下具体怎么使用吧。</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	本章具体代码请参考 springmvc-chapter1工程。</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	&nbsp;</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	<a href="http://sishuok.com/" style="padding: 0px; margin: 0px; color: rgb(0, 94, 167); text-decoration: none; line-height: 1.5em; ">私塾在线学习网</a>原创内容（<a href="http://sishuok.com/" style="padding: 0px; margin: 0px; color: rgb(0, 94, 167); text-decoration: none; line-height: 1.5em; " target="_blank">http://sishuok.com</a>）</p>
<p style="padding: 0px; margin: 0px; font-size: 14px; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; ">
	原创内容，转载请注明私塾在线【<a href="http://sishuok.com/forum/blogPost/list/5050.html#15499" style="padding: 0px; margin: 0px; color: rgb(0, 94, 167); text-decoration: none; line-height: 1.5em; ">http://sishuok.com/forum/blogPost/list/5050.html</a>】</p>

