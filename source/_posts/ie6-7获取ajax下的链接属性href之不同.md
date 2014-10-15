title: ie6/7获取ajax下的链接属性href之不同
tags:
  - "html&css"
  - jQuery
  - solution
date: 2011-08-09 15:00:43
---

ajax载入的一段html，当在IE6/7下去获取这段html中的&lt;a&gt;属性href，并且href值为锚标记，会以当前url置前。

例如： &lt;a href=&#8221;#demo&#8221;&gt;ajax载入的链接&lt;/a&gt; ， 当前浏览器地址栏为: http://localhost/a.html

ie6/7获取结果：  http://localhost/a.html#demo

非ie6/7获取结果：  #demo