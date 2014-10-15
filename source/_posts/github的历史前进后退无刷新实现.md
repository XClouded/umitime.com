title: github的历史前进后退无刷新实现
tags:
  - "html&css"
  - solution
  - html5 history api
  - pajax
date: 2011-07-05 15:45:12
---

今天google了下，了解了解。

Github的源码浏览在点击浏览器前进或后退时，会呈现滑动特效，并且地址栏url真实改变，整个页面无刷新，与传统的加#url的实现完全不一样了。

这得益于HTML5的history api加强。其他人已经分析写的很好了，再此贴上参考资料：

*   [HTML5 History API ](http://html5demos.com/history)
*   [Ajax保留浏览器历史的两种解决方案（Hash&amp;Pjax）](http://leyteris.iteye.com/blog/1107597)
*   [一个实现这种效果的jquery插件：pushState + ajax = pjax](https://github.com/defunkt/jquery-pjax/)

有空可以玩玩。