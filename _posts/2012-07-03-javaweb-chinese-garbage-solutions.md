---
layout: post
title: JavaWeb开发 中文乱码解决方案总结
date: 2012-07-03 13:28
author: onecoder
comments: true
categories: [JSP, Web开发, 中文, 乱码]
---
<div style="padding: 0px; margin: 0px auto; font-size: 12px; border-width: 0px; overflow: hidden; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; text-align: -webkit-left;  ">
	<strong style="padding: 0px; margin: 0px; ">中文乱码：</strong>在以后学习过程中全部采用UTF-8</div>
<div style="padding: 0px; margin: 0px auto; font-size: 12px; border-width: 0px; overflow: hidden; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; text-align: -webkit-left;  ">
	<strong style="padding: 0px; margin: 0px; ">1、文件的乱码</strong></div>
<div style="padding: 0px; margin: 0px auto; font-size: 12px; border-width: 0px; overflow: hidden; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; text-align: -webkit-left;  ">
	<strong style="padding: 0px; margin: 0px; ">1.1、项目文本文件默认编码：</strong></div>
<div style="padding: 0px; margin: 0px auto; font-size: 12px; border-width: 0px; overflow: hidden; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; text-align: -webkit-left;  ">
	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 【右击项目】-&gt;【Properties】-&gt;【Resource】-&gt;【Text file encoding】&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</div>
<div style="padding: 0px; margin: 0px auto; font-size: 12px; border-width: 0px; overflow: hidden; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; text-align: -webkit-left;  ">
	&nbsp;</div>
<div style="padding: 0px; margin: 0px auto; font-size: 12px; border-width: 0px; overflow: hidden; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; text-align: -webkit-left;  ">
	<strong style="padding: 0px; margin: 0px; ">1.2、文件默认编码：默认使用项目的默认编码</strong></div>
<div style="padding: 0px; margin: 0px auto; font-size: 12px; border-width: 0px; overflow: hidden; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; text-align: -webkit-left;  ">
	&nbsp; &nbsp;&nbsp; 【右击文件】-&gt;【Properties】-&gt;【Resource】-&gt;【Text file encoding】</div>
<div style="padding: 0px; margin: 0px auto; font-size: 12px; border-width: 0px; overflow: hidden; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; text-align: -webkit-left;  ">
	<strong style="padding: 0px; margin: 0px; ">1.3、</strong>&nbsp;<strong style="padding: 0px; margin: 0px; ">JSP</strong>&nbsp;<strong style="padding: 0px; margin: 0px; ">文件编码：由于</strong>&nbsp;<strong style="padding: 0px; margin: 0px; ">JSP</strong>&nbsp;<strong style="padding: 0px; margin: 0px; ">要翻译为</strong>&nbsp;<strong style="padding: 0px; margin: 0px; ">Servlet</strong></div>
<div style="padding: 0px; margin: 0px auto; font-size: 12px; border-width: 0px; overflow: hidden; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; text-align: -webkit-left;  ">
	1.3.1、JSP文件编码：</div>
<div style="padding: 0px; margin: 0px auto; font-size: 12px; border-width: 0px; overflow: hidden; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; text-align: -webkit-left;  ">
	&nbsp;&nbsp;&nbsp;&nbsp; 【右击文件】-&gt;【Properties】-&gt;【Resource】-&gt;【Text file encoding】</div>
<div style="padding: 0px; margin: 0px auto; font-size: 12px; border-width: 0px; overflow: hidden; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; text-align: -webkit-left;  ">
	1.3.2、JSP翻译为Servlet时的编码：</div>
<div style="padding: 0px; margin: 0px auto; font-size: 12px; border-width: 0px; overflow: hidden; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; text-align: -webkit-left;  ">
	&nbsp;&nbsp; &lt;%@ page language=&quot;java&quot; pageEncoding=&quot;utf-8&quot;%&gt;</div>
<div style="padding: 0px; margin: 0px auto; font-size: 12px; border-width: 0px; overflow: hidden; font-family: verdana, arial, helvetica, sans-serif; line-height: 21px; text-align: -webkit-left;  ">
	<div class="O" style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
		<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
			中文乱码：在以后学习过程中全部采用UTF-8</div>
		<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
			<strong style="padding: 0px; margin: 0px; ">2、浏览器和服务器间传输数据的乱码</strong></div>
		<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
			&nbsp;</div>
		<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
			<strong style="padding: 0px; margin: 0px; ">2.1、</strong>&nbsp;<strong style="padding: 0px; margin: 0px; ">浏览器端</strong>&nbsp;<strong style="padding: 0px; margin: 0px; ">编码</strong>，一般由服务器端告诉浏览器如何解码数据：</div>
		<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
			<strong style="padding: 0px; margin: 0px; ">2.1.1、Servlet</strong></div>
		<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
			&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;response.setContentType(&quot;text/html; charset=UTF-8&quot;);</div>
		<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
			&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 或者&nbsp; response.setCharacterEncoding(&ldquo;UTF-8&rdquo;);</div>
		<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
			<strong style="padding: 0px; margin: 0px; ">&nbsp;&nbsp; 2.1.2、JSP</strong></div>
		<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
			&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &lt;%@ page language=&quot;java&quot; contentType=&quot;text/html; charset=utf-8&quot; %&gt;</div>
		<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
			<strong style="padding: 0px; margin: 0px; ">&nbsp;&nbsp; 2.1.3、通用</strong></div>
		<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
			&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &lt;meta http-equiv=&quot;Content-Type&quot; content=&quot;text/html; charset=utf-8&quot;&gt;</div>
		<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
			&nbsp;</div>
		<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
			注：浏览器编码可以由我们手工修改，但最后不要这样，一般让浏览器自动选择即可。</div>
		<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
			<div class="O" style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
				<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
					中文乱码：在以后学习过程中全部采用UTF-8</div>
				<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
					<strong style="padding: 0px; margin: 0px; ">2、浏览器和服务器间传输数据的乱码</strong></div>
				<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
					&nbsp;</div>
				<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
					<strong style="padding: 0px; margin: 0px; ">2.2、</strong>&nbsp;<strong style="padding: 0px; margin: 0px; ">服务器端</strong>&nbsp;<strong style="padding: 0px; margin: 0px; ">编码</strong>，将客户端传过来的数据进行解码：</div>
				<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
					&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 浏览器默认使用ISO-8859-1进行编码数据，然后将数据传输到服务器，因此我们默认只需要将浏览器发送过来的数据转换为我们需要的编码即可。</div>
				<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
					<strong style="padding: 0px; margin: 0px; ">2.2.1、最简单方式：</strong></div>
				<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
					&nbsp;&nbsp;&nbsp; String username = request.getParameter(&ldquo;username&rdquo;);</div>
				<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
					&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; username =&nbsp;<strong style="padding: 0px; margin: 0px; ">new String(username.getBytes(&quot;ISO-8859-1&ldquo;), &quot;UTF-8&quot;);</strong></div>
				<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
					<strong style="padding: 0px; margin: 0px; ">&nbsp;&nbsp; 2.2.2、比较好的解决方案</strong></div>
				<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
					<strong style="padding: 0px; margin: 0px; ">&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;</strong>//&nbsp;<strong style="padding: 0px; margin: 0px; "><em style="padding: 0px; margin: 0px; ">必须</em></strong>&nbsp;<strong style="padding: 0px; margin: 0px; ">在获取参数之前</strong>，调用如下方法先解码</div>
				<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
					<strong style="padding: 0px; margin: 0px; ">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; request.setCharacterEncoding(&ldquo;UTF-8&rdquo;);</strong></div>
				<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
					&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;String username = request.getParameter(&ldquo;username&rdquo;);</div>
				<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
					<div class="O" style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
						<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
							<strong style="padding: 0px; margin: 0px; ">2.4、比较通用方案：在一个Filter中更改所有请求的编码方式：</strong></div>
						<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
							&nbsp;&nbsp;&nbsp; 1、在src中添加filter来设置编码格式是中文，filter类可以在tomcat的包里面：apache-tomcat-6.0.16.zip\apache-tomcat-6.0.16\webapps\examples\WEB-INF\classes\filters下面，SetCharacterEncodingFilter.java文件</div>
						<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
							&nbsp;</div>
						<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
							<strong style="padding: 0px; margin: 0px; ">&nbsp;&nbsp;&nbsp;&nbsp; 2</strong>&nbsp;<strong style="padding: 0px; margin: 0px; ">、在</strong>&nbsp;<strong style="padding: 0px; margin: 0px; ">web.xml</strong>&nbsp;<strong style="padding: 0px; margin: 0px; ">中配置</strong>&nbsp;<strong style="padding: 0px; margin: 0px; ">filter</strong>&nbsp;<strong style="padding: 0px; margin: 0px; ">，具体配置如下页：</strong></div>
						<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
							&lt;filter&gt;</div>
						<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
							&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &lt;filter-name&gt;Set Character Encoding&lt;/filter-name&gt;</div>
						<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
							&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &lt;filter-class&gt;filters.SetCharacterEncodingFilter&lt;/filter-class&gt;</div>
						<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
							&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &lt;init-param&gt;&lt;param-name&gt;encoding&lt;/param-name&gt;</div>
						<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
							&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &lt;param-value&gt;utf-8&lt;/param-value&gt;&lt;/init-param&gt;</div>
						<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
							&lt;/filter&gt;</div>
						<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
							&lt;filter-mapping&gt;</div>
						<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
							&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &lt;filter-name&gt;Set Character Encoding&lt;/filter-name&gt;</div>
						<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
							&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &lt;url-pattern&gt;/*&lt;/url-pattern&gt;</div>
						<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
							&lt;/filter-mapping&gt;</div>
						<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
							<div class="O" style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
								<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
									<strong style="padding: 0px; margin: 0px; ">2.5、&nbsp;</strong>GET提交方式中文乱码解决</div>
								<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
									<strong style="padding: 0px; margin: 0px; ">通过修改</strong>request.setCharacterEncoding(&ldquo;UTF-8&rdquo;);只能解决POST提交方式的请求编码，对GET无效。</div>
								<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
									&nbsp;</div>
								<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
									&nbsp;&nbsp;&nbsp;&nbsp; 1&nbsp;<strong style="padding: 0px; margin: 0px; ">、最简单解决方案：</strong>&nbsp;<strong style="padding: 0px; margin: 0px; ">该</strong>解决方案不再需要任何额外配置（如filter）</div>
								<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
									&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; TOMCAT默认ISO-8859-1 因此可以设置默认编码为UTF-8解决，在conf\server.xml文件中设置如下</div>
								<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
									&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &lt;Connector port=&quot;8080&quot; protocol=&quot;HTTP/1.1&quot;</div>
								<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
									&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; connectionTimeout=&quot;20000&quot;</div>
								<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
									&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; redirectPort=&quot;8443&quot; URIEncoding=&rdquo;UTF-8&rdquo;/&gt;</div>
								<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
									<strong style="padding: 0px; margin: 0px; ">&nbsp;&nbsp;&nbsp;&nbsp; 2</strong>&nbsp;<strong style="padding: 0px; margin: 0px; ">、在</strong>&nbsp;<strong style="padding: 0px; margin: 0px; ">tomcat4</strong>&nbsp;<strong style="padding: 0px; margin: 0px; ">之后，可以通过如下配置来解决中文乱码，但必须配置</strong>&nbsp;<strong style="padding: 0px; margin: 0px; ">filter</strong></div>
								<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
									&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 在conf\server.xml文件中设置如下</div>
								<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
									&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &lt;Connector port=&quot;8080&quot; protocol=&quot;HTTP/1.1&quot;</div>
								<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
									&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; connectionTimeout=&quot;20000&quot;</div>
								<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
									&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; redirectPort=&quot;8443&quot; useBodyEncodingForURI=&ldquo;true&rdquo;&nbsp; /&gt;</div>
								<div style="padding: 0px; margin: 0px auto; border-width: 0px; overflow: hidden; ">
									&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 然后request.setCharacterEncoding(&ldquo;UTF-8&rdquo;)也可解决GET乱码<br />
									<br />
									转自：<a href="http://sishuok.com/forum/blogPost/list/306.html">http://sishuok.com/forum/blogPost/list/306.html</a></div>
							</div>
						</div>
					</div>
				</div>
			</div>
		</div>
	</div>
</div>

