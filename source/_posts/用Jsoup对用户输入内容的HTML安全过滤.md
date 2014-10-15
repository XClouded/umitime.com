title: 用Jsoup对用户输入内容的HTML安全过滤
tags:
  - java
  - solution
  - filter script
  - script标签过滤
  - 内容安全过滤
  - 用户内容过滤
date: 2011-12-14 14:44:41
---

在网站使用input或textarea提供给用户可输入内容的功能，比如发帖子，发文章，发评论等等。这时候需要后端程序对输入内容作安全过滤，比如&lt;script&gt;等可造成安全隐患的标签。

java中有个开源包叫Jsoup，本身用来解析html，xml文档的，特点是可以使用类似jquery的选择权语法。

最近在解决内容安全过滤的时候，通过google发现Jsoup通过自定义Whitelist（安全标签白名单）提供了这样的功能，非常好用。

<span id="more-561"></span>

简单演示如下：

<pre class="brush:java">//HTML clean
String unsafe = "&lt;table&gt;&lt;tr&gt;&lt;td&gt;1&lt;/td&gt;&lt;/tr&gt;&lt;/table&gt;" +
		"&lt;img src='' alt='' /&gt;" +
		"&lt;p&gt;&lt;a href='http://example.com/' onclick='stealCookies()'&gt;Link&lt;/a&gt;" +
		"&lt;object&gt;&lt;/object&gt;" +
		"&lt;script&gt;alert(1);&lt;/script&gt;" +
		"&lt;/p&gt;";
String safe = Jsoup.clean(unsafe, Whitelist.relaxed());
System.out.println("safe: " + safe);</pre>

官方API地址： http://jsoup.org/apidocs/org/jsoup/safety/Whitelist.html

发现来源：

http://www.oschina.net/question/12_10232 ， 据此自己写了个自定义的帮助类：

<pre class="brush:java">package com.cssor.safety;

import org.jsoup.Jsoup;
import org.jsoup.helper.StringUtil;
import org.jsoup.safety.Whitelist;

public class ContentSafeFilter {
	private final static Whitelist user_content_filter = Whitelist.relaxed();
	static {
		//增加可信标签到白名单
		user_content_filter.addTags("embed","object","param","span","div");
		//增加可信属性
		user_content_filter.addAttributes(":all", "style", "class", "id", "name");
		user_content_filter.addAttributes("object", "width", "height","classid","codebase");
		user_content_filter.addAttributes("param", "name", "value");
		user_content_filter.addAttributes("embed", "src","quality","width","height","allowFullScreen","allowScriptAccess","flashvars","name","type","pluginspage");
	}

	/**
	 * 对用户输入内容进行过滤
	 * @param html
	 * @return
	 */
	public static String filter(String html) {
		if(StringUtil.isBlank(html)) return "";
		return Jsoup.clean(html, user_content_filter);
		//return filterScriptAndStyle(html);
	}

	/**
	 * 比较宽松的过滤，但是会过滤掉object，script， span,div等标签，适用于富文本编辑器内容或其他html内容
	 * @param html
	 * @return
	 */
	public static String relaxed(String html) {
		return Jsoup.clean(html, Whitelist.relaxed());
	}

	/**
	 * 去掉所有标签，返回纯文字.适用于textarea，input
	 * @param html
	 * @return
	 */
	public static String pureText(String html) {
		return Jsoup.clean(html, Whitelist.none());
	}

	/**
	 * @param args
	 */
	public static void main(String[] args) {
		String unsafe = "&lt;table&gt;&lt;tr&gt;&lt;td&gt;1&lt;/td&gt;&lt;/tr&gt;&lt;/table&gt;" +
				"&lt;img src='' alt='' /&gt;" +
				"&lt;p&gt;&lt;a href='http://example.com/' onclick='stealCookies()'&gt;Link&lt;/a&gt;" +
				"&lt;object&gt;&lt;/object&gt;" +
				"&lt;script&gt;alert(1);&lt;/script&gt;" +
				"&lt;/p&gt;";
		String safe = ContentSafeFilter.filter(unsafe);
		System.out.println("safe: " + safe);
	}

}</pre>

Jsoup不支持相对路径图片的过滤，比如&lt;img src=&#8221;/1.png&#8221; alt=&#8221;&#8221; /&gt;会被去掉src属性，想了个简单的方法避免：

<pre class="brush:java">/**
 * 自定义对用户输入内容进行过滤的标签
 * @param html
 * @return
 */
public static String filter(String html) {
    if(StringUtil.isBlank(html)) return "";
    String baseUri = "http://baseuri";
    return Jsoup.clean(html, baseUri, user_content_filter).replaceAll("src=\"http://baseuri", "src=\"");
}</pre>