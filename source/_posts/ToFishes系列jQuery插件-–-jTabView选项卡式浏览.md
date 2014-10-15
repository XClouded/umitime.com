title: ToFishes系列jQuery插件 – jTabView选项卡式浏览
tags:
  - jQuery
  - jquery plugin
  - jTabView
  - 选项卡插件
date: 2010-08-11 10:08:06
---

网上有不少jQuery的选项卡插件，官方的tab貌似也不错，但用来用去哪有自己写的顺手。

该插件实际使用于公司旗下网站 &#8211; 易扑网（eapoo.com）， 在网站开发过程中编写，同时扩展了不少功能。

本插件遵循 结构、表现、行为 相分离的原则，所以，html结构与css样式（决定UI）请自己决定。

[![jTabView选项卡式浏览插件截图](http://demo.cssor.com/jtabview/screenshot.png "jTabView选项卡式浏览插件截图")](http://demo.cssor.com/jtabview/ "查看jTabView选项卡式浏览插件Demo及下载")

<span id="more-48"></span>

#### 快速入门：

首先编写html，比如下例：

<pre class="html">&lt;div class="jtabview"&gt; &lt;!-- or has id="jtabview" --&gt;
 &lt;a class="jtab"&gt;tab1&lt;/a&gt;
 &lt;a class="jtab"&gt;tab2&lt;/a&gt;
 ...more tags &lt;a class="jtab"&gt;
 &lt;div class="jtab-con"&gt;content1&lt;/div&gt;
 &lt;div class="jtab-con"&gt;content2&lt;/div&gt;
 ...more tags &lt;div class="jtab-con"&gt;
&lt;/div&gt;</pre>

然后js调用（别忘了引入jQuery文件先）：

<pre class="js">$(function(){
 $(".jtabview, #jtabview... ...").jTabView({ //支持多个选择器，前提是它们有相同的参数配置，以下是可以配置的参数
 tab:".jtab", //选项卡标题, 选择器子元素,允许为jQuery对象，例如tab:$(".jtab")
 con:".jtab-con", // 选项卡对应的内容元素，确保选项卡数与内容数一致。选择器子元素,允许为jQuery对象，例如con:$(".jtab-con")
 event: "click", //设置触发tab切换的事件，默认是单击，也可以设定为mousehover,这样鼠标悬浮就会切换
 curr: "curr", //设置当前激活中的选项卡样式类

 auto: 0, //是否自动切换， 默认否
 time: 5000, //自动切换的时间间隔， 默认5秒
 hoverTime: 300, //如果是鼠标悬浮切换，为了提高体验，设置一定的延迟然后再触发切换

 retain: 0, //在浏览器刷新的情况下，是否记忆保持选项卡的激活位置，这样可以防刷新，默认不开启,依赖jquery.cookie插件
 expires:1, //设置记忆的过期时间，过了这个时间防刷新失效，默认1天

 accordion: 0 //设置开启手风琴风格的切换，点击当前项会在显示与隐藏之间切换，默认否
 });
});</pre>

上面已经罗列了可以配置的默认参数，可以有选择的设置。其中最主要的就是 tab，con参数，应该属于应用了插件的元素的子元素，因为提供了可设置为jQuery对象（有点鸡肋吧，当时想着应用广泛，就加上了），此时就无此限制。

另外需要说明一个参数：curr，它的存在既是为了当前显示项的状态类，也是初始需要显示第N项的参数。 比如说tab项目是这样的结构：&lt;a &gt;选项1&lt;/a&gt;&lt;a  class=&#8221;curr&#8221;&gt;选项2&lt;/a&gt;&lt;a &gt;选项3&lt;/a&gt;，在选项2加了curr，那么初始化会先显示第二项的内容。

这里并没有提供css，是为了更多的自由，要写个样式并不难。

写的是简单了些，因为是自己写的插件，太熟了，还真是不好写。

[点击这里查看Demo或下载](http://demo.cssor.com/jtabview/ "jQuery选项卡插件jTabView演示与下载")

感觉复杂，推荐阅读：[jTabView选项卡插件的简易原理版](http://www.cssor.com/jtabview-simple-implement.html "jTabView选项卡插件的简易原理版")