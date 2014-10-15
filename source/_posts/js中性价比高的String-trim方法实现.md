title: js中性价比高的String.trim方法实现
tags:
  - 自然
  - js trim
date: 2013-12-02 18:36:25
---

来源于：http://www.cnblogs.com/rubylouvre/archive/2009/09/18/1568794.html

js中去除字符串首尾空格效率及代码优雅度比较高的方法：

<pre>String.prototype.trim = function() {
  var str = this,
  str = str.replace(/^\s\s*/, ''),
  ws = /\s/,
  i = str.length;
  while (ws.test(str.charAt(--i)));
  return str.slice(0, i + 1);
}</pre>