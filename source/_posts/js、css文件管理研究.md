title: js、css文件管理研究
tags:
  - solution
  - css
  - js
  - web文件管理
date: 2010-11-02 11:06:49
---

### <span style="color: #008000;">1、同名子目录下的自动引入</span>

这个思想是这样的，在/css, /js 等根目录下，根据不同channel， module的URL访问，自动在合适位置引入与channel，module同名的子目录中的文件

比如两种访问：二级目录URL：www.cssor.com/news/..，www.cssor.com/shopping/&#8230;；二级域名URL：news.cssor.com/&#8230;，shopping.cssor.com/&#8230;;

那么这些URL下的页面自动去引入(link href, script src)或包含(php/jsp,include)位于/css,/js下面同名子目录中的文件，也就是：/css/news/, /css/shopping/, /js/news/,/js/shopping下的所有文件。

由此可以引申出许多有效的同名管理策略。