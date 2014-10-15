title: 目前可用的CSS3圆角属性(border-radius)详细记录
tags:
  - "html&css"
  - css3
  - css3 corner
  - css3圆角
date: 2010-09-09 16:45:22
---

来源：http://blog.nixternal.com/2009.06.19/css-border-radius/

CSS3的圆角支持，不同浏览器有不同的css属性实现。偶个人最喜欢Opera ，它是严格按照标准css3属性实现的。

属性详单：

### <span style="color: #008000;">FireFox（火狐浏览器，FF）:</span>

<pre class="brush:css">/* 5px四角全部是圆角 */
-moz-border-radius: 5px;
/* 5px左上、右下角的圆角
 注： 四个值代表从左上角顺时针方向的各个角 */
-moz-border-radius: 5px 0 5px 0;
/* 5px坐下，右上角的圆角 */
-moz-border-radius: 0 5px 0 5px;
/* 5px左上圆角*/
-moz-border-radius-topleft: 5px;
/* 5px左下圆角 */
-moz-border-radius-bottomleft: 5px;
/* 5px右上圆角 */
-moz-border-radius-topright: 5px;
/* 5px右下圆角*/
-moz-border-radius-bottomright: 5px;<span id="more-130"></span></pre>

### <span style="color: #008000;">CSS3标准属性（Opera支持）：</span>

<pre class="brush:css">/* 5px radius on all 4 corners of the table */
border-radius: 5px;
/* 5px radius on top left and bottom right corners only */
border-radius: 5px 0 5px 0;
/* 5px radius on bottom left and top right corners only */
border-radius: 0 5px 0 5px;
/* 5px radius on the top left corner only */
border-top-left-radius: 5px;
/* 5px radius on the bottom left corner only */
border-bottom-left-radius: 5px;
/* 5px radius on the top right corner only */
border-top-right-radius: 5px;
/* 5px radius on the bottom right corner only */
border-bottom-right-radius: 5px;</pre>

### <span style="color: #008000;">Webkit （chrome，Safari）：</span>

<pre class="brush:css">/* 只要在标准css3属性前面增加 -webkit- 前缀即可 */
-webkit-border-top-right-radius: 5px;</pre>

### <span style="color: #008000;">KHTML (Konqueror，是linux系统的KDE桌面环境所带的浏览器)：</span>

<pre class="brush:css">/* 只要在标准css3属性前面增加 -khtml- 前缀即可*/
-khtml-border-radius: 5px;</pre>