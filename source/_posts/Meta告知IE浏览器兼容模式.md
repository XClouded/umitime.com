title: Meta告知IE浏览器兼容模式
tags:
  - "html&css"
  - 浏览器兼容模式
date: 2012-07-20 11:21:41
---

在html的head头内如下写法，节选自http://www.xingzai.org/html-note/meta-tag-usage-order.html：

<pre>&lt;!--x-ua-compatible(浏览器兼容模式)
仅对IE8+以效
告诉浏览器以什么版本的IE的兼容模式来显示网页
&lt;meta http-equiv="X-UA-Compatible" content="IE=5" &gt;
&lt;meta http-equiv="X-UA-Compatible" content="IE=7" &gt;
&lt;meta http-equiv="X-UA-Compatible" content="IE=8" &gt;
&lt;meta http-equiv="X-UA-Compatible" content="IE=edge" &gt;
其中最后一行是永远以最新的IE版本模式来显示网页的。
另外加上Emulate模式
&lt;meta http-equiv="X-UA-Compatible" content="IE=EmulateIE7" &gt;
&lt;meta http-equiv="X-UA-Compatible" content="IE=EmulateIE8" &gt;
Emulate模式后则更重视&lt;!DOCTYPE&gt;
（细心的人会注意到，用IE9去访问带有x-ua-compatible的页面时是不会出现兼容视图按钮的）
--&gt;
&lt;meta http-equiv="X-UA-Compatible" content="IE=EmulateIE8" /&gt;</pre>