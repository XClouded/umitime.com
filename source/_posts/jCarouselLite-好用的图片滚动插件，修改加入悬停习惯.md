title: jCarouselLite-好用的图片滚动插件，修改加入悬停习惯
tags:
  - code
  - jQuery
date: 2011-04-13 23:02:33
---

插件Home：http://www.gmarwaha.com/jquery/jcarousellite/index.php

一直在用，并且没有想过自己再造轮子的插件，作用是图片列表上下或垂直滚动，可以添加方向控制按钮，你应该懂的。

用法很简单，jquery插件通用的套路，具体看官网。个人对这个插件做了下修改，当设置为自动滚动时候，鼠标放上去可以停止，离开则继续，同时增加一个button inactive类名设置。下面进入正题。

<span id="more-244"></span>

对于html的结构要求是这样的：

<pre class="brush:xml">&lt;div id="handler"&gt;
    &lt;ul&gt;
        &lt;li&gt;&lt;img /&gt;&lt;/li&gt;
         ……
    &lt;/ul&gt;
&lt;/div&gt;</pre>

div,ul都会被插件自动加入合适的css，而且是相对定位。基本不需要额外css控制，当然在js生效前，会乱，如果网速慢的话。

有必要对div加额外的margin等，控制其居中或怎样。div的left属性为0，是插件自动设置的，所以不要通过这个去控制位置了。

插件修改后的代码如下：

<pre class="brush:js">(function($) {   // Compliant with jquery.noConflict()
$.fn.jCarouselLite = function(o) {
    o = $.extend({
        btnPrev: null,
        btnNext: null,
        btnGo: null,
        btnDisabled: 'btn-inactive', //禁用的时候的样式类名
        mouseWheel: false,
        auto: null,   

        speed: 200,
        easing: null,   

        vertical: false,
        circular: true,
        visible: 3,
        start: 0,
        scroll: 1,   

        beforeStart: null,
        afterEnd: null
    }, o || {});   

    return this.each(function() {                           // Returns the element collection. Chainable.   

        var running = false, animCss=o.vertical?"top":"left", sizeCss=o.vertical?"height":"width";
        var div = $(this), ul = $("ul", div), tLi = $("li", ul), tl = tLi.size(), v = o.visible;   

            if(tl == 0) {
                return false;
            }
        if(o.circular) {
            ul.prepend(tLi.slice(tl-v-1+1).clone())
              .append(tLi.slice(0,v).clone());
            o.start += v;
        }   

        var li = $("li", ul), itemLength = li.size(), curr = o.start;
        div.css("visibility", "visible");   

        li.css({overflow: "hidden", 'float': o.vertical ? "none" : "left"});
        ul.css({margin: "0", padding: "0", position: "relative", "list-style-type": "none", "z-index": "1"});
        div.css({overflow: "hidden", position: "relative", "z-index": "2", left: "0px"});   

        var liSize = o.vertical ? height(li) : width(li);   // Full li size(incl margin)-Used for animation
        var ulSize = liSize * itemLength;                   // size of full ul(total length, not just for the visible items)
        var divSize = liSize * v;                           // size of entire div(total length for just the visible items)   

        li.css({width: li.width(), height: li.height()});
        ul.css(sizeCss, ulSize+"px").css(animCss, -(curr*liSize));   

        div.css(sizeCss, divSize+"px");                     // Width of the DIV. length of visible images   

        if(o.btnPrev)
            $(o.btnPrev).click(function() {
                return go(curr-o.scroll);
            });   

        if(o.btnNext)
            $(o.btnNext).click(function() {
                return go(curr+o.scroll);
            });   

        if(o.btnGo)
            $.each(o.btnGo, function(i, val) {
                $(val).click(function() {
                    return go(o.circular ? o.visible+i : i);
                });
            });   

        if(o.mouseWheel &amp;&amp; div.mousewheel)
            div.mousewheel(function(e, d) {
                return d&gt;0 ? go(curr-o.scroll) : go(curr+o.scroll);
            });
        var jcarouselliteAutoId;
        if(o.auto) {
            jcarouselliteAutoId = setInterval(function() {
                go(curr+o.scroll);
            }, o.auto+o.speed);
            /* 2009年7月30日 添加鼠标浮动停止滚动 */
            ul.hover(function(){
                    clearInterval(jcarouselliteAutoId);
                },function(){
                    jcarouselliteAutoId = setInterval(function() {
                        go(curr+o.scroll);
                    }, o.auto+o.speed);
            });
        };   

        function vis() {
            return li.slice(curr).slice(0,v);
        };   

        function go(to) {
            if(tl &lt;= 3) {
                return false;
            }   

            if(!running) {   

                if(o.beforeStart)
                    o.beforeStart.call(this, vis());   

                if(o.circular) {            // If circular we are in first or last, then goto the other end
                    if(to&lt;=o.start-v-1) {           // If first, then goto last
                        ul.css(animCss, -((itemLength-(v*2))*liSize)+"px");
                        // If "scroll" &gt; 1, then the "to" might not be equal to the condition; it can be lesser depending on the number of elements.
                        curr = to==o.start-v-1 ? itemLength-(v*2)-1 : itemLength-(v*2)-o.scroll;
                    } else if(to&gt;=itemLength-v+1) { // If last, then goto first
                        ul.css(animCss, -( (v) * liSize ) + "px" );
                        // If "scroll" &gt; 1, then the "to" might not be equal to the condition; it can be greater depending on the number of elements.
                        curr = to==itemLength-v+1 ? v+1 : v+o.scroll;
                    } else curr = to;
                } else {                    // If non-circular and to points to first or last, we just return.
                    if(to&lt;0 || to&gt;itemLength-v) return;
                    else curr = to;
                }                           // If neither overrides it, the curr will still be "to" and we can proceed.   

                running = true;   

                ul.animate(
                    animCss == "left" ? { left: -(curr*liSize) } : { top: -(curr*liSize) } , o.speed, o.easing,
                    function() {
                        if(o.afterEnd)
                            o.afterEnd.call(this, vis());
                        running = false;
                    }
                );
                // Disable buttons when the carousel reaches the last/first, and enable when not
                if(!o.circular) {
                    $(o.btnPrev + "," + o.btnNext).removeClass(o.btnDisabled);
                    $( (curr-o.scroll&lt;0 &amp;&amp; o.btnPrev)
                        ||
                       (curr+o.scroll &gt; itemLength-v &amp;&amp; o.btnNext)
                        ||
                       []
                     ).addClass(o.btnDisabled);
                }   

            }
            return false;
        };
    });
};   

function css(el, prop) {
    return parseInt($.css(el[0], prop)) || 0;
};
function width(el) {
    return  el[0].offsetWidth + css(el, 'marginLeft') + css(el, 'marginRight');
};
function height(el) {
    return el[0].offsetHeight + css(el, 'marginTop') + css(el, 'marginBottom');
};   

})(jQuery);</pre>

2011年7月12日 附加一个demo代码：

html结构：

<pre class="brush:xml">&lt;div class="slider-wrap"&gt;
    &lt;a id="bg_store_prev" class="carousel-button carousel-prev" href="javascript:void(0);"&gt;&lt;/a&gt;
    &lt;a id="bg_store_next" class="carousel-button carousel-next" href="javascript:void(0);"&gt;&lt;/a&gt;
    &lt;div <span style="color: #ff0000;">id="store-bg-wrap"</span>&gt;
        &lt;ul class="slider-list clearfix"&gt;&lt;?php for($i = 0; $i &lt; 5; $i++) { //php循环输出多个图片li，省事儿 ?&gt;
            &lt;li&gt;
                &lt;a class="thumb-wrap" &gt;
                    &lt;img src="http://wac.2659.edgecastcdn.net/802659/production82/images/backgrounds/miguel_gonzalez_passing_by.png"&gt;
                &lt;/a&gt;
                &lt;p class="background-sig"&gt;Passing Wall&lt;br /&gt;by &lt;a href=""&gt;Miguel Gonzalez&lt;/a&gt;&lt;/p&gt;
            &lt;/li&gt;                            &lt;?php }?&gt;
        &lt;/ul&gt;
    &lt;/div&gt;
&lt;/div&gt;</pre>

css样式：

<pre class="brush:css">.slider-wrap {position:relative;padding:0 22px 0 24px;zoom:1;height:142px;overflow:hidden}
    .carousel-button{position:absolute;top:0;width:17px;height:100px;}
    .carousel-prev{left:0;background:#ddd url(http://wac.2659.edgecastcdn.net/802659/production83/images/buttons/sprite_carousel_prev.png) no-repeat 4px 50%;border-radius:3px 0 3px 0;-moz-border-radius:3px 0 3px 0;-webkit-border-radius:3px 0 3px 0;}
    .carousel-next{right:0;background:#ddd url(http://wac.2659.edgecastcdn.net/802659/production83/images/buttons/sprite_carousel_next.png) no-repeat 4px 50%;border-radius:0px 3px 0px 3px;-moz-border-radius:0 3px 3px 0;-webkit-border-radius:0 3px 3px 0 ;}
    .carousel-prev:hover{background-position:-16px 50%}
    .carousel-next:hover{background-position:-16px 50%}
    .carousel-prev:active{background-position:-36px 50%}
    .carousel-next:active{background-position:-36px 50%}
    .btn-inactive {background-position:-56px 50% !important}
.slider-list li {display:inline;float:left;position:relative;margin:0 6px 6px 0;width:160px;text-align:center;}
    .thumb-wrap img {width:156px;height:96px;border:2px solid #fff}
    .thumb-wrap:hover img, .thumb-wrap-curr img {border-color:#2b82ad}</pre>

js调用：

<pre class="brush:js">$('#store-bg-wrap').jCarouselLite({
    circular: 0,
    btnPrev: '#bg_store_prev',
    btnNext: '#bg_store_next'
});</pre>