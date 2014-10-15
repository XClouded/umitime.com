title: ToFishes系列jQuery插件之jSuggest – 搜索框输入框自动下拉提示内容补全插件
tags:
  - jQuery
  - jsuggest
  - suggest
  - 下拉提示
  - 自动提示补全
date: 2012-05-31 18:24:51
---

很久前就完成的一款插件，应用于高人网(gaoren.com)自动补全提示功能。

该插件只托管了键盘各种事件，输入框change事件，及ajax请求过程。下拉提示内容如何显示，及数据请求后的格式解析等均可以自己自定义，可以说如何显示，如何安排数据格式，完全由心。算是比较灵活了，当然灵活的代价就是配置项，需要写的比较多了。

还没想好插件的使用帮助，先放出demo和下载。有个bug是opera最新版下，按住方向键，下拉选项不能连续选择。

在输入框内输入内容，可以获取到来自淘宝数据的提示：

<input id="auto-suggest" type="text" name="q" />

[点击查看单独Demo和和获取下载](http://demo.cssor.com/jsuggest/ "ToFishes jQuery系列插件之jSuggest - 搜索框输入框自动下拉提示内容补全插件")，已更新至V2。