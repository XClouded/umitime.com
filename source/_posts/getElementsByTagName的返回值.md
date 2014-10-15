title: getElementsByTagName的返回值
tags:
  - JavaScript
date: 2012-08-09 16:23:11
---

getElementsByTagName返回值标准定义应该是NodeList对象，不是Array。拥有length属性。

在IE一些版本，不可以对返回值做 for in循环，length属性会被循环出来，所以必须 for i++经典遍历。

更多情况，进入传送门：

http://topic.csdn.net/u/20100706/14/a81cdeed-7112-4c3e-8408-540c4951879c.html