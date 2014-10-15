title: ToFishes系列jQuery插件之bindFor – 简单jQuery事件绑定辅助插件
tags:
  - jQuery
  - solution
  - event bind
date: 2011-02-19 06:57:01
---

源于认识到对[closest用法](http://www.cssor.com/jquery-api-closeset-context.html "jquery closest method")的错误使用（closet的第二个参数context，目前只能是dom element，不能是jquery对象），以及一直想实现bindFor对本身的事件绑定支持，此辅助插件做了一次大修订。

源码如下：

<pre class="brush:js">/**
 * @author ToFishes
 * @date 2011-1-13
 * @desc 针对ajax载入的元素方便事件绑定的插件（注意：只能绑定可以冒泡的事件类型， 这是插件功能的实现原理）
 * @update 2011-5-16 修复closest用法上的错误 _e.closest(key, this); 此处是this dom对象，而非 $(this) jquery对象
 *
 * 将ajax内容需要绑定事件的元素，将事件绑定到非ajax载入的父级元素， 通过冒泡原理实现事件绑定，有一定限制
 * 能够在一定程度上提高jQuery事件绑定效率
 *
 * Example:
 * 	$("#parents").bindFor("click", {
 *  	"#demo": function(event){
 *  		alert(this.html());
 *  		alert(e);
 *  	},
 *        '_self': function(event){} //_self不是一个选择器，仅仅是为了给自身绑定事件
 * 	});
 *
 * html:
 * 	&lt;div id="parents"&gt;
 * 		&lt;p id="demo"&gt;这里是ajax动态载入的内容data
 * 			比如： $.get(url, function(data){ $("#parents").html(data);   })
 * 		&lt;/p&gt;
 * 	&lt;/div&gt;
 */
;(function($){
    $.bindFor = $.fn.bindFor = function(event, map){
    	var $o = this.jquery ? this : $(document),
    			event = event || "click",
    			map = map || {};

    	return $o.bind(event, function(e){
    		var _e = $(e.target);

    		for(var key in map) {
    			var $t = jQuery();
    			if(key === '_self') {
    				$t = $(this);
    			} else if(_e.is(key)) {
    				$t = _e;
    			} else {
    				if(e.target !== this) { //如果等于自身，closest的context参数就会失去本身的意义，不再拥有范围限定
    					$t = _e.closest(key, this);
    				};
    			};

    			if($t.length) {
    				return map[key].call($t, e);
    			};
    		};
    	});
    };
})(jQuery);

</pre>

PS.  jQuery 1.4.2+新增.delegate()方法，跟此辅助插件一个性质，但是bindFor提供map形式的绑定，对事件密集元素用起来还是很爽的。不过没提供unBindFor&#8230;基本没这种需求

<pre class="brush:js">
</pre>

<span style="text-decoration: line-through;">/**  * @author ToFishes  * @date 2011-1-13  * @desc 1、针对ajax载入的元素方便事件绑定（注意：只能绑定可以冒泡的事件类型， 这是插件功能的实现原理）  *       2、某父级元素的子元素很多并且都需要绑定同样事件，可以只一次绑定到父元素  * 将ajax内容需要绑定事件的元素，将事件绑定到非ajax载入的父级元素， 通过冒泡原理实现事件绑定，有一定限制  * 能够在一定程度上提高jQuery事件绑定效率  *  * Example:  * 	$(&#8220;#parents&#8221;).bindFor(&#8220;click&#8221;, {  *  	&#8220;#demo&#8221;: function(event){  *  		alert(this.html());  *  		alert(e);  *  	}  * 	});  *  * html:  * 	&lt;div id=&#8221;parents&#8221;&gt;  * 		&lt;p id=&#8221;demo&#8221;&gt;这里是ajax动态载入的内容data  * 			比如： $.get(url, function(data){ $(&#8220;#parents&#8221;).html(data);   })  * 		&lt;/p&gt;  * 	&lt;/div&gt;  */ ;(function($){     $.bindFor = $.fn.bindFor = function(event, map){     	var $o = this.jquery ? this : $(document),     			event = event || &#8220;click&#8221;,     			map = map || {};      	return $o.bind(event, function(e){     		var _e = $(e.target);     		for(var key in map) {     			var $t = _e.is(key) ? _e : _e.closest(key, $(this));     			if($t.length) {     				map[key].call($t, e);     				break;     			};     		};     	});     }; })(jQuery);</span>