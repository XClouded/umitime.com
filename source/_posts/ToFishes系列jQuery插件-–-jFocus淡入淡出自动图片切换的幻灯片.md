title: ToFishes系列jQuery插件 – jFocus淡入淡出自动图片切换的幻灯片
tags:
  - jQuery
  - jfoucs
  - jquery plugin
  - jquery幻灯片插件
  - jquery自动切换
  - 淡入淡出的幻灯片
date: 2010-08-18 09:01:12
---

这个插件相对来说灵活性一般，而且js对html有些地方侵入操作，工作中针对UI效果图开发的。

看下截图：

[![jFocus插件截图](http://demo.cssor.com/jfocus/screenshot.jpg "jFocus插件截图")](http://demo.cssor.com/jfocus/ "查看jFocus淡入淡出幻灯片插件Demo及下载")

因为灵活性不高，除非对css比较熟悉，所以这里放出完整的html结构，css样式定义。

<span id="more-6"></span>

<span style="color: #008000;">首先看html：</span>

<pre class="brush:xml">&lt;!-- 结构理论上自定义，但需要两个class指定元素： .img 和 .infos，这两个不可少 --&gt;
&lt;div class="focus"&gt;
	&lt;ul&gt;
		&lt;li&gt;&lt;img class="img" src="img/tmp-focus1.jpg" alt="" /&gt;
			&lt;div class="infos"&gt;
				&lt;h6&gt;&lt;a href="" title=""&gt;上海世博建设全接触&lt;/a&gt;&lt;/h6&gt;
				&lt;p class="desc"&gt;这图的描述，随便点，想描述什么描述什么，但是是个简述，字数不要太多的好&lt;/p&gt;
			&lt;/div&gt;
		&lt;/li&gt;
		&lt;li&gt;&lt;img class="img" src="img/tmp-focus2.jpg" alt="" /&gt;
			&lt;div class="infos"&gt;
				&lt;h6&gt;&lt;a href="" title=""&gt;绿叶丛中隐藏的小猎手&lt;/a&gt;&lt;/h6&gt;
				&lt;p class="desc"&gt;这图的描述，随便点，想描述什么描述什么，但是是个简述，字数不要太多的好&lt;/p&gt;
			&lt;/div&gt;
		&lt;/li&gt;
		&lt;li&gt;&lt;img class="img" src="img/tmp-focus0.jpg" alt="" /&gt;
			&lt;div class="infos"&gt;
				&lt;h6&gt;&lt;a href="" title=""&gt;Elva的壁纸秀&lt;/a&gt;&lt;/h6&gt;
				&lt;p class="desc"&gt;这图的描述，随便点，想描述什么描述什么，但是是个简述，字数不要太多的好&lt;/p&gt;
			&lt;/div&gt;
		&lt;/li&gt;
	&lt;/ul&gt;
&lt;/div&gt;</pre>

<span style="color: #008000;">然后是css:</span>

<pre class="brush:css">/* 另外自备css reset，不知道css reset? Google一下 */
.jfocus{ position:relative; overflow:hidden;}
.jfocus li { position:absolute; top:0; left:0;}
.jfocus .infos, .jfocus .overlay, .jfocus .index { position:absolute; bottom:0;  z-index:2;  }
	.jfocus .infos { left:0; padding-left:10px;  }
		.jfocus .infos a, .jfocus .infos .desc {margin:0 0 10px;color:#ff0;}
	/* overlay修改opacity属性可以更改透明度，ie则修改filter的opacity */
	.jfocus .overlay { left:0; height: 60px; width:100%; filter:alpha(opacity=50);
			opacity:0.5; background:#000; z-index:1;}
	.jfocus .index { padding:0 10px 10px; height: auto; right:0; text-align:right; }
		.jfocus .index a { display:inline-block; margin:0 5px; color:#fff;
				font-weight:700; font-family:tahoma; }
		.jfocus .index a.curr { color:#f00; }
.jfocus em {font-style: normal;  }</pre>

<span style="color: #008000;">调用js:</span>

<pre class="brush:js">&lt;script type="text/javascript" src="http://ajax.googleapis.com/ajax/libs/jquery/1.3.2/jquery.min.js"&gt;&lt;/script&gt;
&lt;script type="text/javascript" src="jFocus-tofishes-1.0.pack.js"&gt;&lt;/script&gt;
&lt;script type="text/javascript"&gt;
&lt;!--
$(function(){
	$(".focus").jFocus({event:"click",auto:1,time:2000});
	/*
	可用的配置项目：
	{
		meta: "li", //指定需要循环切换的元素标签
		event: "click", //绑定触发切换的事件，比如设置为mouseover
		currClass: "curr", //当前数字高亮的class
		firstShow: 0, //指定第一次显示的图片索引，从0开始
		width: 0, //设置一个宽度，默认0代表自动获取宽度，如果获取失败可以尝试手动设置
		height:0, //设置一个高度，默认0代表自动获取高度，如果获取失败可以尝试手动设置

		auto: 0, //是否开启自动切换,默认否
		time: 3000 //自动切换的时间间隔
	}

	*/
});
//--&gt;
&lt;/script&gt;</pre>

[点击这里查看Demo及下载](http://demo.cssor.com/jfocus/ "ToFishes系列jQuery插件-jFocus淡入淡出的幻灯片")。