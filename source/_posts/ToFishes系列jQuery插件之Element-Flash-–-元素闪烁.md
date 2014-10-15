title: ToFishes系列jQuery插件之Element Flash – 元素闪烁
tags:
  - jQuery
  - jquery flash
  - jquery plugin
  - tofishes
  - 元素闪烁效果
date: 2010-12-14 06:51:37
---

先给代码：

<pre class="brush:js">/**
 * @author ToFishes
 * 根据指定css属性闪烁的插件
 */
;(function($){
	$.fn.jFlash = function(c){
		c = $.extend({
			css: {}, //闪烁变化的css属性
			time: 100, //interval time 闪烁间隔时间
			count: 20, //闪烁的次数
			status: "jFlashing", //表示闪烁中
			onStop: function(t){} //闪烁完的回调函数，传入闪烁对象的引用
		}, c);

		return this.each(function(){
			var _t = $(this), init = {};
			/* 如果正在闪烁中，则回避再次调用闪烁 */
			if(_t.hasClass(c.status))
				return;

			for(var n in c.css) {
				init[n] = _t.css(n);
			};

			var params = {
				"0": init,
				"1": c.css
			},  flashId, count = c.count % 2 == 0 ? c.count : c.count + 1; //count保证为偶，这样最后的css才是最初的状态。

			function flash(){
				_t.css(params[count % 2]).addClass(c.status);
				count--;
				if(count &lt; 0) {
					c.onStop(_t.removeClass(c.status));
					flashId &amp;&amp; clearTimeout(flashId);
					return;
				};
				flashId = setTimeout(flash, c.time);
			};
			flash();
		});
	};
})(jQuery);

</pre>

距离上午发布的代码没多久，现在是晚间10点，偶对代码又做了下修改，但是同时偶觉得上面的代码可以算是另外一种解决方案，因为闪烁不会被中途打断。现在修改后，也只是改了下闪烁中途可以打断并执行另外一段闪烁而已。

同时，做了个Demo页面，[查看Demo点击这里 ](http://demo.cssor.com/jflash/ "jFlash 元素闪烁插件Demo - ToFishes").

Ok，修改后的代码如下：

<pre class="brush:js">
/**
 * @author ToFishes
 * @version 1.0
 * 根据指定css属性闪烁的插件
 */
;(function($){
    $.fn.jFlash = function(c){
        c = $.extend({
            css: {}, //闪烁变化的css属性
            time: 100, //interval time 闪烁间隔时间
            count: 20, //闪烁的次数
            status: "jFlashing", //表示闪烁中
            onStop: function(t){} //闪烁完的回调函数，传入闪烁对象的引用
        }, c);

        return this.each(function(){
            var _t = $(this), init = _t.data("initCss") || {};
            /* 如果正在闪烁中，则清除当前的，继续调用下一个闪烁 */
            if(_t.hasClass(c.status)) {
				_t.css(init);
                clearTimeout(_t.data("jFlashId") || 0);
			};

	        for(var n in c.css) {
                init[n] = _t.css(n);
            };
			_t.data("initCss", init);

            var params = {
                "0": init,
                "1": c.css
            },  flashId, count = c.count % 2 == 0 ? c.count : c.count + 1; //count保证为偶，这样最后的css才是最初的状态。

            function flash(){
                _t.css(params[count % 2]).addClass(c.status);
                count--;
                if(count &lt; 0) {
                    c.onStop(_t.removeClass(c.status));
                    return;
                };
                flashId = setTimeout(flash, c.time);
				_t.data("jFlashId", flashId);
            };
            flash();
        });
    };
})(jQuery);</pre>