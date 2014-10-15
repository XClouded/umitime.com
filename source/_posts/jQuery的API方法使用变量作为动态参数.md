title: jQuery的API方法使用变量作为动态参数
tags:
  - jQuery
  - jQuery动态参数
  - 对象key为变量
date: 2010-10-25 14:44:13
---

jQuery很多方法是可以传入一个对象做参数的，例如 $.get(url, {name: &#8220;aa&#8221;})， $.animate({width:&#8221;300px&#8221;}, 300 );等等这些。

可是实际使用时候，直接把{}参数写入方法，则{}的key是固定的，不可以是变量。

举例，偶写个ajax提交的form，假设全是文本框的值，有如下文本框：

<pre class="brush:xml">&lt;form&gt;
&lt;input name="name1" value="" /&gt;
&lt;input name="n2" value="" /&gt;
&lt;input name="nam3" value="" /&gt;
.....

&lt;/form&gt;</pre>

难道提交的时候，要这样写吗： $.ajax{url, {name1: value, n2: value, nam3: value &#8230;}}, 要知道，每个input的name属性不是固定的，如果有动态生成的input则更加糟糕。

解决：

<pre class="brush:js">var o = {}; //动态的参数o
var $i = $("input");
$i.each(function(){
    var name = $(this).attr("name");
    var value = $(this).val();
    o[name] = value; //{}的key用[]来作为变量
});

//提交ajax
$.post(url,   o , callback);</pre>

同理，很多插件效果，使用animate时候，可能只需要改变一个属性，比如指定top（向上）还是bottom（向下）,就可以避免写死参数的key，达到一段代码重用的作用。

第二种参数传递，bind()和trigger()之间的参数传递：

<pre class="brush:js">$("p").bind("click",  function (event, a, b) {
  // 一个普通的点击事件时，a和b是undefined类型
  // 如果用下面的语句触发，那么a指向"foo",而b指向"bar"
} ).trigger("click", ["foo", "bar"]); 

/*
以上是jQuery手册的原句，.click修改为了.bind()而已, 初次使用小郁闷了一下，
一定要注意trgger传参是数组，例如[a, b, c]，bind接受参数是function(event, a, b, c)，
 第一个一定是event事件的引用，其次依次是参数数组的值列，一一对应
*/</pre>