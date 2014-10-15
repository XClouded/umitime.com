title: js判断文档是否包含某标签字符串的不靠谱
tags:
  - jQuery
date: 2010-11-19 12:12:41
---

写了一段js，目的呢，是判断某个div的html内是否包含某个标签：

<pre class="brush:js">
var a = "&lt;a rel='1122'&gt;demo&lt;/a&gt;";
var html = $("div").html;
console.info(html.indexOf(a) == -1);
if(html.indexOf(a) == -1)
    $("div").append(a); //本意就是这样，如果发现不存在该标签就append到这个div
</pre>

结果程序运行有bug，后来发现，这种方式及其不靠谱，偶忽视了html获取在各个浏览器是不一样的结果。比如FF获取到的标签属性是加双引号的， IE却是大写标签，并且属性无引号，很显然偶的这种写法大错特错了。

记录之以警示！