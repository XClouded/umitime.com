title: jTabView选项卡插件的简易原理版
tags:
  - jQuery
  - jQuery Tab
  - 简单jQuery选项卡插件
date: 2010-08-19 09:54:39
---

发布了jTabView插件，看着代码好像很复杂，原理却很简单，其他的功能都是围绕这个做的扩展。

如果不需要那么复杂的功能，用起来倒是杀鸡用牛刀了，而且代码也不简洁。以下就是核心实现代码，做了一个简单tab插件。

<pre class="brush:js">/* 简单选项卡插件 */
;(function($){
    $.fn.TabSimple = function(c){
        c = $.extend({
            tab: ".s-tab", //标题项
            con: ".s-con", //内容项
            curr: "curr",  //当前高亮的css类名
            event: "click" //触发的事件，jQuery所支持的所有事件
        },c);
        return this.each(function(){
            var tab = $(this).find(c.tab);
            var con = $(this).find(c.con);
            tab.bind(c.event, function(){
                var i = tab.index($(this));
                tab.removeClass(c.curr); //或tab.find("." + c.curr).removeClass(c.curr)
                $(this).addClass(c.curr);
                con.hide().eq(i).show();
            });
        });
    };
})(jQuery);</pre>

HTML结构自定义了，跟[ToFishes系列jQuery插件 – jTabView选项卡式浏览](http://www.cssor.com/jquery-plugin-jtabview.html)所述一致。

2010-10-14，Release Update V2：

<pre class="brush:js">;(function($){
    $.fn.jTabSimple = function(c){
        c = $.extend({
            tab: ".tabs li", //标题项
            con: ".tab-con", //内容项
            curr: "curr",  //当前高亮的css类名
            index: 1,	   //默认显示的tab
            remote: false, //远程加载
            loading: null, //自定义loading的提示html片段
            event: "click" //触发的事件，jQuery所支持的所有事件
        },c);

        return this.each(function(){
            var tab = $(this).find(c.tab);
            var con = $(this).find(c.con);
            var index = tab.index(tab.filter("." + c.curr)) + 1;
            if(index == 0)
            	index = c.index;
            tab.bind(c.event, function(){
            	var i = tab.index($(this));
            	return toggle(i);
            });
            function toggle(i){
               	tab.removeClass(c.curr).eq(i).addClass(c.curr);
            	if(c.remote){
                    var loading = c.loading ||  '&lt;div class="loading"&gt;&lt;/div&gt;';
                	con.html(loading);
                	$.get(tab.eq(i).find("a").attr("href"), function(ret){
                		con.html(ret);
                	});

                	return false;
                } else {
                	con.hide().eq(i).show();
                }
            };
            toggle(index - 1);
        });

    };
})(jQuery);</pre>