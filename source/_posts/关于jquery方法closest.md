title: 关于jquery方法closest()
tags:
  - jQuery
  - closest method
date: 2011-04-29 14:39:21
---

最近发现jquery 1.4中文手册中对于closest()方法的context描述有误。

中文手册的描述：

> #### closest(expr,[context] )
> 
> ##### 参数
> 
> **expr **(String, Array) : 用以过滤元素的表达式。jQuery  1.4开始，也可以传递一个字符串数组，用于查找多个元素。
> 
> **context (可选) **(Element, jQuery) : 作为待查找的 DOM  元素集、文档<span style="color: #ff0000;">或 jQuery 对象。</span><span style="color: #ff0000;"> </span>

其中描红的文字“jquery对象”，是错误的用法，官方文档没有说明可以是jquery对象（实际测试，也确实不可以是jquery对象）。

附上官方文档说明(官方仅指出是一个dom element)：

> #### .closest( selector, [ context ] )
> 
> selector       A string containing a selector expression to match elements against.
> 
> context        <span style="color: #008000;">**<span style="text-decoration: underline;">A DOM element</span>** </span>within which a matching element may be found. If no context is passed in then the context of the jQuery set will be used instead.

PS. 另外发现一个特性：

<pre class="brush:xml">&lt;body&gt;
    &lt;ul&gt;
        &lt;li&gt;
            &lt;p id="test"&gt;test context for this&lt;/p&gt;
        &lt;/li&gt;
    &lt;/ul&gt;
&lt;/body&gt;</pre>

当使用$(&#8220;#text&#8221;).closest(&#8216;li&#8217;, $(&#8220;#text&#8221;)[0])时，即context为元素本身，则会继续向上寻找，返回li，而不是空元素。