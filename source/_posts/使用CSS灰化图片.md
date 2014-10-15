title: 使用CSS灰化图片
tags:
  - "html&css"
  - grayscale
  - 灰化
date: 2014-05-07 12:07:48
---

截止到目前，比较兼容的方案：

<pre>.gray { 
  -webkit-filter: grayscale(100%);
  -ms-filter: grayscale(100%);
  -o-filter: grayscale(100%);
  filter: url("data:image/svg+xml;utf8,&lt;svg xmlns=\'http://www.w3.org/2000/svg\'&gt;&lt;filter id=\'grayscale\'&gt;&lt;fecolormatrix type=\'matrix\' values=\'0.3333 0.3333 0.3333 0 0 0.3333 0.3333 0.3333 0 0 0.3333 0.3333 0.3333 0 0 0 0 0 1 0\'&gt;&lt;/fecolormatrix&gt;&lt;/filter&gt;&lt;/svg&gt;#grayscale"); /* Firefox 10+, Firefox on Android */
  filter: grayscale(100%);
  filter: gray;
}</pre>

同时有个一个js方案可供选择：http://www.iliasiovis.com/hoverizr/

资料：

https://developer.mozilla.org/en-US/docs/Web/CSS/filter#Browser_compatibility

该资料说明Gecko核心的Firefox仅实现了filter:url()这一个滤镜功能。所以上面可以通过svg来实现FF的兼容。

http://www.karlhorky.com/2012/06/cross-browser-image-grayscale-with-css.html