title: js原生支持base64编码与解码
tags:
  - JavaScript
  - base64
date: 2013-12-02 23:43:29
---

base64编码方法： window.btoa(); 解码方法：window.atob();

<pre>var a = btoa('hello world!')
var b = atob(a)
console.info(a,'****', b)</pre>

仔细看就发现这个方法名字很好记： b to a, a to b