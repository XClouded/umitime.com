title: spring3.1.1 mvc使用注解搭建hello world
tags:
  - java
  - spring mvc annotation
  - spring3.1
  - spring3.1 mvc
date: 2012-04-12 18:49:07
---

使用spring3.1.1中的mvc搭建项目，写一个hello world出来。

看了两天官方文档，不管好坏，就是组织的比较零散，不能有序的给人一个项目基本的轮廓。而且里面的例子也不是最佳实践。

开发工具是Eclipse 3.7，服务器是tomcat7，用的是jee3.0标准，新建一个 Dynamic Web Project，随便命名为cms。

<span id="more-689"></span>

然后往classpath中加入最基本的jar包（最简单自然是把spring下的所有包都导入），其实就是把spring的jar包复制到项目下的WEB-INF/lib目录即可（偶尔有意外者，可再去配置buildpath，手动加入jar包），筛选后的基本jar包如图所示：

[![](http://cssor.com/wp-content/uploads/2012/04/spring3-mvc-jars-300x238.png "spring3 mvc jars")](http://cssor.com/wp-content/uploads/2012/04/spring3-mvc-jars.png)

&nbsp;

相关jar包添加完毕后，开始做基础配置，首先修改web.xml（上图也可以看见）文件如下：

<pre class="brush:xml">&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xmlns:web="http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd" id="WebApp_ID" version="3.0"&gt;
	&lt;display-name&gt;cms&lt;/display-name&gt;
	&lt;welcome-file-list&gt;
		&lt;welcome-file&gt;index.html&lt;/welcome-file&gt;
		&lt;welcome-file&gt;index.jsp&lt;/welcome-file&gt;
	&lt;/welcome-file-list&gt;

	&lt;servlet&gt;
	    &lt;servlet-name&gt;cms&lt;/servlet-name&gt;
	    &lt;servlet-class&gt;org.springframework.web.servlet.DispatcherServlet&lt;/servlet-class&gt;
	    &lt;load-on-startup&gt;1&lt;/load-on-startup&gt;
	&lt;/servlet&gt;

	&lt;servlet-mapping&gt;
	    &lt;servlet-name&gt;cms&lt;/servlet-name&gt;
	    &lt;url-pattern&gt;/&lt;/url-pattern&gt;
	&lt;/servlet-mapping&gt;
&lt;/web-app&gt;</pre>

就是配置一个DispatcherServlet，其url-pattern是/，而不是/*，需要注意这点。/表示DispatcherServlet为当前缺省servlet，凡是在web.xml文件中找不到匹配的&lt;servlet-mapping&gt;元素的URL，它们的访问请求都将交给缺省Servlet处理。而/*会处理所有的请求，不管有没有其他的&lt;servlet-mapping&gt;匹配。举例说，如果在根目录有这样一个文件/a.html，那么访问 http://localhost/a.html，使用配置/，则不会被spring mvc处理，直接访问到a.html，如果使用配置/*，那么spring mvc就会进行处理，没有相应handler，则会报错。这样可以避免静态文件，样式文件，图片，js文件等被spring mvc处理，而出现找不到的情况。

web.xml配置完成，根据spring mvc默认的约定，还需在WEB-INF目录下新建一个cms-serlvet.xml ，命名不是随意的，而是根据DispatcherServlet在web.xml中所对应的servlet-name来命名（跟项目名无关，虽然所建项目名也是cms），结构为 [servlet-name]-servlet.xml。

[![](http://cssor.com/wp-content/uploads/2012/04/spring-mvc-config-file-300x99.png "spring mvc config file")](http://cssor.com/wp-content/uploads/2012/04/spring-mvc-config-file.png)

cms-servlet.xml配置基于Annotation（注解）的方式，内容如下，

&nbsp;

<pre class="brush:xml">&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;beans xmlns="http://www.springframework.org/schema/beans" 
    xmlns:mvc="http://www.springframework.org/schema/mvc"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p" 
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="

http://www.springframework.org/schema/beans

http://www.springframework.org/schema/beans/spring-beans-3.0.xsd

http://www.springframework.org/schema/mvc

http://www.springframework.org/schema/mvc/spring-mvc-3.1.xsd

http://www.springframework.org/schema/context

        http://www.springframework.org/schema/context/spring-context-3.0.xsd"&gt;

    &lt;context:component-scan base-package="com.cssor.controller"/&gt;
    &lt;mvc:annotation-driven /&gt;

    &lt;bean class="org.springframework.web.servlet.view.UrlBasedViewResolver"&gt;
        &lt;property name="viewClass" value="org.springframework.web.servlet.view.JstlView"/&gt;
        &lt;property name="prefix" value="/WEB-INF/view/"/&gt;
        &lt;property name="suffix" value=".jsp"/&gt;
    &lt;/bean&gt;
&lt;/beans&gt;</pre>

其中jsp文件是放在 /WEB-INF/view/目录下，context:component-scan定义了spring自动扫描基础包路径，该包下的类会被srping自动扫描管理，基于annotation。mvc:annotation-driven启动注解驱动的mvc。

配置都完成，开始写Controller，新建一个类，Main.java，内容如下：

<pre class="brush:java">package com.cssor.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

@Controller
public class Main {

	@RequestMapping(value="/hello")
	public ModelAndView handleRequest() {

		ModelAndView mav = new ModelAndView("hello");
		mav.addObject("what", "Hello World!");

		return mav;
	}

}</pre>

然后在上文配置过的视图指定目录/WEB-INF/view/下建立hello.jsp，注意jsp文件名是跟上文中Main.classs所写的 new ModelAndView(&#8220;hello&#8221;)是对应关系，可不是乱起的，是同名关系。hello.jsp内容如：

<pre class="brush:xml">&lt;%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%&gt;
&lt;!DOCTYPE html&gt;
&lt;html lang="zh-CN"&gt;
&lt;head&gt;
&lt;meta charset="UTF-8"&gt;
&lt;title&gt;Insert title here&lt;/title&gt;
&lt;/head&gt;
&lt;body&gt;
&lt;h2&gt;${what }&lt;/h2&gt;
&lt;/body&gt;
&lt;/html&gt;</pre>

然后部署项目到tomcat，启动tomcat，访问 http://localhost/hello，就可以看到输出了。至此，基本环境算是搭好了。

参考：http://starscream.iteye.com/blog/1058693

http://hi.baidu.com/С%B6%BE%B3%E6q/blog/item/42cce919a656f80934fa4110.html。