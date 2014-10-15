title: 用Web字体实现小图标
tags:
  - "html&css"
  - font
  - icon
date: 2012-08-30 14:19:41
---

如果用github，或许会发现github上的小图标不是用背景图做的，并且可以选中，类似文字一样。

[![](http://cssor.com/wp-content/uploads/2012/08/font-icons.png "font-icons")](http://cssor.com/wp-content/uploads/2012/08/font-icons.png)

原理就是把图标做成了字体，然后用@font-face引入。这种方式确实不错，图标大小和颜色都可以css定义了，虽然不会太炫丽，但是也不错，可以在firebug下修改试试。

相关信息传送至：

https://github.com/blog/1106-say-hello-to-octicons

https://github.com/styleguide/css/7.0