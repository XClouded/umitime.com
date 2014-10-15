title: jQuery配合正则取背景图地址
tags:
  - jQuery
  - solution
date: 2011-06-15 17:29:57
---

来自于： http://webdevel.blogspot.com/2009/07/jquery-quick-tip-extract-css-background.html

一个方法函数：

<pre class="brush:js">function extractUrl(input)
{
 // remove quotes and wrapping url()
 return input.replace(/"/g,"").replace(/url\(|\)$/ig, "");
}</pre>

然后使用  extractUrl($(&#8220;#myelement&#8221;).css(&#8220;background-image&#8221;))