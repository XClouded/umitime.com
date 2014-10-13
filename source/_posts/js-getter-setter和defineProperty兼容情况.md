title: js getter setter和defineProperty兼容情况
tags:
  - JavaScript
date: 2014-09-17 12:07:59
---

测试数据源于： [http://robertnyman.com/javascript/javascript-getters-setters.html](http://robertnyman.com/javascript/javascript-getters-setters.html)

兼容ie9+及其他标准浏览器的写法有二：

1、

<pre>
var lost = {
	loc : "Island",
	get location () {
		return this.loc;
	},
	set location(val) {
		this.loc = val;
	}
};
lost.location = "Another island";
</pre>

2、

<pre>
var lost = {
	loc : "Island"
};	
Object.defineProperty(lost, "location", {
	get : function () {
		return this.loc;
	},
	set : function (val) {
		this.loc = val;
	}
});
lost.location = "Another island";
</pre>