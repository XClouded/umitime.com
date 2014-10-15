title: jQuery的事件命名空间-Namespaced Events
tags:
  - jQuery
  - jquery事件命名空间
  - namespaced events
date: 2011-05-05 10:26:21
---

命名空间简单易懂的说就是为了避免混淆，恰似人类的姓+名。专业解释请看 [http://zh.wikipedia.org/wiki/命名空间](http://zh.wikipedia.org/wiki/命名空间 "新页面打开")。

命名就是取名，比如两个人都叫做小明，空间就是区分命名的，比如一个小明姓王，一个小明姓张。王，张的姓就是空间，命名+空间（姓，名合体：王+小明，张+小明），你就不会混淆到底在说哪个小明。

jquery的事件命名空间，就是为了区分同一个jquery元素绑定的多个同名事件，使用方法就是: $.bind(&#8220;事件名.区分后缀&#8221;, function)，$.trigger(&#8220;事件名.区分后缀&#8221;)。下面仅以unbind举例说明，trigger也是同样道理：

<pre class="brush:js">&lt;script type="text/javascript"&gt;
$(function(){
    $("body").bind('click', function(){
        console.info('第一次单击绑定输出');
    });
    $("body").bind('click', function(){
        console.info('第二次单击绑定输出');
    });
    $("#unbind").click(function(){
        $('body').unbind('click');
        console.info('解除了body的所有单击事件');
    });
});
&lt;/script&gt;</pre>

以上代码绑定了两次click事件，会同时执行。unbind(&#8216;click&#8217;)，就会解除全部的click事件。

如果我只想解除其中之一，而不是解除所有的单击事件，怎么办？答案就是使用命名空间。看以下代码：

<pre class="brush:js">&lt;script type="text/javascript"&gt;
$(function(){
    $("body").bind('click.one', function(){
        console.info('第一次单击绑定输出');
    });
    $("body").bind('click.two', function(){
        console.info('第二次单击绑定输出');
    });
    $("#unbind").click(function(){
        $('body').unbind('click.one');
        console.info('只解除body的第一次单击事件');
    });
    $("#unbind-all").click(function(){
        $('body').unbind('click');
        console.info('解除了body的全部单击事件，包括有命名空间的单击事件');
    });
});
&lt;/script&gt;</pre>

当解除click.one时候，click.two事件就被保留了下来。但是unbind(&#8216;click&#8217;)会解除所有（无论有没有命名空间）的单击事件。

简单的说： 杀死王小明，那么张小明会活下来。杀死小明，那么代表王小明和张小明都要被干掉。

所以，jquery的事件命名空间就是提供一种途径，让你恰当的对指定事件解除绑定(unbind)或触发(trigger)，而不影响其他已绑定的“同名不同姓”的事件。

PS. 官方说明 http://docs.jquery.com/Namespaced_Events

在jquery1.2+就已经可以使用命名空间。在jquery1.3+以后，还可以这样绑定：

<pre class="brush:js">$('.class').bind('click.a.b', function(){});
$('.class').trigger('click.a');
$('.class').unbind('click.b');
//经测试 click.a.b === click.a === click.b 这样的命名空间有什么用？囧，貌似曾用名，现用名，但还是同一个人。
</pre>

另外看看这样：

<pre class="brush:js">
$("a").bind("click.one", function)
$("a").bind("mouseover.one", function)
$("a").unbind(".one"); //结果会怎样呢？</pre>
<pre class="brush:js"></pre>