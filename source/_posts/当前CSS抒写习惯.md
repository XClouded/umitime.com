title: 当前CSS抒写习惯
tags:
  - code
  - "html&css"
  - css
  - css reset
  - css属性顺序
date: 2011-12-07 19:42:55
---

保持了火狐工程师推荐的属性书写顺序习惯：

<pre class="brush:css">/* mozilla.org Base Styles
* maintained by fantasai
* (classes defined in the Markup Guide - http://mozilla.org/contribute/writing/markup )
*/
/* Suggested order:
//显示属性
* display
* list-style
* position
* float
* clear
//自身属性
* margin
* padding
* width
* height
* border
* background
//文本属性
* color
* font
* text-decoration
* text-align
* vertical-align
* white-space
* other text
* content
*
*/</pre>

&nbsp;

惯用的css reset写法及常用类：

<span id="more-537"></span>

<pre class="brush:css">body, h1, h2, h3, h4, h5, h6, hr, p, blockquote, dl, dt, dd,
        ul, ol, li, pre, form, fieldset, legend, button, input, textarea,th, td { margin: 0;  padding: 0; }
input,select {vertical-align:middle;font-size:100%}
h2 {font-size:18px}
h3 { font-size:14px; }
h4,h5,h6 { font-size: 12px; color:#444}
address, cite, em, dfn{ font-style: normal; }
ul, ol { list-style: none; }
a { cursor: pointer;text-decoration: none;color:#F36C21;}
a.plain { color: #555; }
a.plain:hover { color: #888; }
q:before, q:after { content: ''; }
fieldset,img,iframe { border: none; }
table { width:100%; border-collapse: collapse; border-spacing: 0; }
pre {white-space:pre-wrap !important}
/* html5 tags */
article, aside, dialog, footer, header, section, footer, nav, figure {display: block}
/* 让非ie浏览器默认也显示垂直滚动条，防止因滚动条引起的闪烁 */
html{overflow-y: scroll; }
body{min-width:1000px;width:auto;background:#fff;font:12px/20px "\5FAE\8F6F\96C5\9ED1", tahoma, arial, helvetica, sans-serif; color:#333;word-wrap:break-word}
/* common tags */
th, td {padding:6px 5px;text-align:left}
img {vertical-align:middle}
input[type=checkbox], input[type=radio]  { padding: 0 } /* for ie9 */
/* layout */
.wrap {margin:0 auto;padding-left:10px;padding-right:10px;width:960px}
.clear, .item-list li {zoom:1}
.clear:after, .item-list li:after {clear:both; content:'\20';display: block}/* clear float */
.clean {overflow:hidden;zoom:1}
a:hover, .light{color:#F68C51}
.l-f{float:left;display:inline}
.r-f {float:right;display:inline}
.t-c {text-align: center}/* 文字居中 */
.t-r {text-align: right}

.desc {color:#888}
.hide { display:none}

.island {margin:30px 0;} /* 上下比较大间隔的块 */
.land {margin:15px 0;}
.aloof {margin-top:20px;}
.bloof {margin-bottom:20px;}

.panel {margin-top:10px;margin-bottom:10px ;}
.apanel {margin-top:10px}
.bpanle {margin-bottom:10px}

.space {margin:0 10px;} /* 左右空间 */
.ispace {margin:0 20px;}
.lspace {margin-left:20px;}
.room {padding:20px 0;} /* 上下内空间 */
.aroom {padding:0 20px;}

.warn {color:#f00}
.empty-con {padding:20px 0;color:#999} /* 为空的内容 */

/* 结构模块化 */
/* 小栏目 */
.col {margin:10px 0;border:1px solid #d3d3d3;}
    .col h3 {position:relative;padding:0 10px;height:30px;line-height:30px;background-position:0 -88px;font-size:12px;color:#666;}
    .con {padding:10px;}

/* 选项卡, IE6,7 body的word-wrap对a有影响，需要重置 */
.tabs {}
	.tabs li { }

/* 列表 */
.item-list li {padding:10px 0}

/* 左图右文字布局 */
.pic {float:left;display:inline;margin:3px 10px 0 0;}
    .pic img {display:block !important; display:inline-block; margin-top:3px}
.txt {overflow:hidden;zoom:1}
/* 头像尺寸，一种控制方式，比较喜欢用background-images方式，这样可以随意控制图片居中 */
.avatar-tiny, .avatar-small, .avatar-medium, .avatar-large {display:inline-block;overflow:hidden}
.avatar-tiny  {width:30px;height:30px}
.avatar-small {width:50px;height:50px}
.avatar-medium{width:100px;height:100px}
.avatar-large {width:160px;height:160px}
	.avatar-tiny img {width:30px}
	.avatar-small img  {height:50px}
	.avatar-medium img {width:100px}
	.avatar-large img  {width:160px}</pre>

2013.12.25： 新增input[type=checkbox], input[type=radio] { padding: 0 } /* for ie9 */， &#8220;\5FAE\8F6F\96C5\9ED1&#8243;替代 微软雅黑 中文，