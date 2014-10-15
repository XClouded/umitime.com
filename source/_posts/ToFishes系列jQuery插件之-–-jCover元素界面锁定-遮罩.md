title: ToFishes系列jQuery插件之 – jCover元素界面锁定(遮罩)
tags:
  - jQuery
  - solution
  - jCover
  - jquery plugin
  - overlay
  - 遮罩
  - 锁定
date: 2011-01-05 08:31:17
---

为啥要写这么个插件，源于jQuery blockUI插件，因为效果基本和这个插件是一样的（我在造轮子。。。）。但是更纯粹倾向于遮罩功能。

但是代码却精简了很多，并且做了优化，减少dom操作。

[![](http://demo.cssor.com/jcover/screenshot.png "jCover插件Demo")](http://demo.cssor.com/jcover/index.html)

PS: 以前也习惯用iframe做IE6遮盖select元素，以为这时候select是会被透明遮盖的，现在才发现iframe遮盖select也是看不见的，相当于设置了visibility: hidden; 所以本插件没有费工夫去写iframe遮盖，iframe远没有css隐藏来的方便。

应用举例：1、先引入jquery及本插件；2、全屏遮罩：$.jCover(); 元素遮罩：$(&#8220;#jCover&#8221;).jCover();

[点击查看Demo并下载](http://demo.cssor.com/jcover/index.html)。