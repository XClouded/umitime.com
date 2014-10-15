title: css3的背景透明如何在IE实现
tags:
  - "html&css"
  - solution
  - CSS3背景透明
  - ie透明背景
  - 半透明背景
  - 透明度和RGB的换算
date: 2011-07-05 12:26:23
---

css3的background-color支持rgba值以支持透明背景，具体使用方法可以看手册或google。

注意不同于css2的opacity属性，opacity 透明会使该元素的子孙元素和文字都变的透明。

标准浏览器如FF，Opera，Safari，Chrome等最新版都支持了rgba的背景透明 ，而仍然使用广泛的IE6,7,8是不支持的。但是今天研究了下about.me，搞清楚了ie下的滤镜实现方式，这样通过一些额外代码就可以使IE也完美表现。

IE下有个背景渐变滤镜：[Gradient Filter](http://msdn.microsoft.com/en-us/library/ms532997(v=vs.85).aspx)。如同Photoshop中的渐变一样。就是这个滤镜可以实现背景透明。<span id="more-283"></span>

基本使用格式如下（详细看这里[Gradient Filter](http://msdn.microsoft.com/en-us/library/ms532997(v=vs.85).aspx)）：

<pre><span style="color: #008000;">**filter: progid:DXImageTransform.Microsoft.gradient(startColorstr=#AARRGGBB,endColorstr=#AARRGGBB);**</span></pre>

<span style="color: #008000;">startColorstr和endColorstr值不相等的时候是渐变， 相等了，就可以实现透明背景。值格式请注意：</span>

<span style="color: #008000;">#AARRGGBB， AA表示透明度，RR,GG,BB就是rgb， 都是16进制，值范围是00~FF，如#00FFFFFF。</span>

<span style="color: #008000;">而css3的background-color: rgba(255,255,255, 0.5) 是10进制表示法，颜色值是0~255之间，透明度值是0~1之间。</span>

看一个例子：

<pre class="brush:xml"> &lt;body id="con" style="background:url(http://d13pix9kaak6wt.cloudfront.net/background/caice_1309833309_14.jpg) no-repeat 50% 0"&gt;
 &lt;div style="filter: progid:DXImageTransform.Microsoft.gradient(startColorstr=#7f7e8f7c,endColorstr=#7f7e8f7c);width:900px;height:300px;border:1px solid #000"&gt;
    真的是是透明的
 &lt;/div&gt;
 &lt;/body&gt;</pre>

在ie下查看可以看到半透明效果。#7f7e8f7c， 第一个7f透明度相当于opacity=0.5， 7e8f7c是很常见的颜色值。

明白这一切之后， 只剩下一个问题， 透明度的换算， 就是进制转换，将常见的0.5换算为‘等价’的16进制的表示法。

就是一个一元一次方程： ![](http://latex.codecogs.com/gif.latex?%5Cfrac{1}{0.5}=%5Cfrac{0xFF}{X})( 1/0.5 = 0xFF/X )，求X值。左边代表opacity，1是最大值，0.5是当前值，右边代表ie滤镜所用的透明度16进制值， 0xFF是最大值，X是所需值。

如果仅仅是在css样式中固定写死的值，可以利用firebug的终端如此输出：  console.info(parseInt((0xFF*0.5)).toString(16))， 然后copy到css中。

而在about.me这样的应用中，是利用js计算所得，并动态更新的。

写一个简单函数 ：

<pre class="brush:js">/**
 * 用于获取IE下半透明的滤镜值
 * @param opacity 小数表示的透明度
 * @param color  16进制颜色值
 */
function getHexOpacity(opacity, color){
    return '#' + parseInt(0xFF * opacity).toString(16) + color;
};
//console.info(getHexOpacity(0.5, 'ff6600')); //输出#7fff6600</pre>

附上从jquery颜色拾取插件colorpicker源码截取的颜色值转换函数：

<pre class="brush:js">fixHSB = function (hsb) {
	return {
		h: Math.min(360, Math.max(0, hsb.h)),
		s: Math.min(100, Math.max(0, hsb.s)),
		b: Math.min(100, Math.max(0, hsb.b))
	};
},
fixRGB = function (rgb) {
	return {
		r: Math.min(255, Math.max(0, rgb.r)),
		g: Math.min(255, Math.max(0, rgb.g)),
		b: Math.min(255, Math.max(0, rgb.b))
	};
},
fixHex = function (hex) {
	var len = 6 - hex.length;
	if (len &gt; 0) {
		var o = [];
		for (var i=0; i&lt;len; i++) {
			o.push('0');
		}
		o.push(hex);
		hex = o.join('');
	}
	return hex;
},
HexToRGB = function (hex) {
	var hex = parseInt(((hex.indexOf('#') &gt; -1) ? hex.substring(1) : hex), 16);
	return {r: hex &gt;&gt; 16, g: (hex &amp; 0x00FF00) &gt;&gt; 8, b: (hex &amp; 0x0000FF)};
},
HexToHSB = function (hex) {
	return RGBToHSB(HexToRGB(hex));
},
RGBToHSB = function (rgb) {
	var hsb = {
		h: 0,
		s: 0,
		b: 0
	};
	var min = Math.min(rgb.r, rgb.g, rgb.b);
	var max = Math.max(rgb.r, rgb.g, rgb.b);
	var delta = max - min;
	hsb.b = max;
	if (max != 0) {

	}
	hsb.s = max != 0 ? 255 * delta / max : 0;
	if (hsb.s != 0) {
		if (rgb.r == max) {
			hsb.h = (rgb.g - rgb.b) / delta;
		} else if (rgb.g == max) {
			hsb.h = 2 + (rgb.b - rgb.r) / delta;
		} else {
			hsb.h = 4 + (rgb.r - rgb.g) / delta;
		}
	} else {
		hsb.h = -1;
	}
	hsb.h *= 60;
	if (hsb.h &lt; 0) {
		hsb.h += 360;
	}
	hsb.s *= 100/255;
	hsb.b *= 100/255;
	return hsb;
},
HSBToRGB = function (hsb) {
	var rgb = {};
	var h = Math.round(hsb.h);
	var s = Math.round(hsb.s*255/100);
	var v = Math.round(hsb.b*255/100);
	if(s == 0) {
		rgb.r = rgb.g = rgb.b = v;
	} else {
		var t1 = v;
		var t2 = (255-s)*v/255;
		var t3 = (t1-t2)*(h%60)/60;
		if(h==360) h = 0;
		if(h&lt;60) {rgb.r=t1;	rgb.b=t2; rgb.g=t2+t3}
		else if(h&lt;120) {rgb.g=t1; rgb.b=t2;	rgb.r=t1-t3}
		else if(h&lt;180) {rgb.g=t1; rgb.r=t2;	rgb.b=t2+t3}
		else if(h&lt;240) {rgb.b=t1; rgb.r=t2;	rgb.g=t1-t3}
		else if(h&lt;300) {rgb.b=t1; rgb.g=t2;	rgb.r=t2+t3}
		else if(h&lt;360) {rgb.r=t1; rgb.g=t2;	rgb.b=t1-t3}
		else {rgb.r=0; rgb.g=0;	rgb.b=0}
	}
	return {r:Math.round(rgb.r), g:Math.round(rgb.g), b:Math.round(rgb.b)};
},
RGBToHex = function (rgb) {
	var hex = [
		rgb.r.toString(16),
		rgb.g.toString(16),
		rgb.b.toString(16)
	];
	$.each(hex, function (nr, val) {
		if (val.length == 1) {
			hex[nr] = '0' + val;
		}
	});
	return hex.join('');
},
HSBToHex = function (hsb) {
	return RGBToHex(HSBToRGB(hsb));
}</pre>