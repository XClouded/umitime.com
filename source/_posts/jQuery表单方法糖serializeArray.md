title: jQuery表单方法糖serializeArray
tags:
  - JavaScript
  - solution
  - serializeArray
date: 2014-09-01 15:05:43
---

jQuery.serializeArray()和jQuery.param()两个表单相关的方法不知道用的人多不多，这两个是获取表单参数的最方便方法。

serializeArray()返回的是一个数组，数组由简单的“名称-值对”对象组成，可以直接当做jQuery.ajax各api方法的data参数穿进去。

例如 

<pre>
var params = $('form').serializeArray();// 返回类似： [{name: 'title', value: '标题'},  {name: 'content', value: '内容值'}]
$.post(url, params, function(){});
</pre>

不过，在实际项目中可能有些变故，比如说我们项目后端要求 对同名参数的多个值是用逗号间隔合并一起的字符串，像checkbox类型，获取多个input[type=checkbox name="hobby"], 使用serailizeArray会返回这样的值：

params = [{ name: "hobby", value: "one" }, { name: "hobby", value: "two" }, { name: "hobby", value: "three" }]

直接用于ajax方法，传参会是这样的： hobby=one&#038;hobby=two&#038;hobby=three

而我们后端要求应该是这样传： hobby=one,two,three

因此直接使用serializeArray不能满足我们的需求，总不能放弃这么好的“方法糖”不吃吧？解决方法简单，我拿serializeArray封装一下，做了另外一个插件，满足了自己的需求，也没增加多少复杂度，具体代码如下：

<pre>
// @tofishes
// var params = $('form').paramMap();  就可以获得整个表单的所有参数
$.fn.paramMap = function (opts) {
    opts = $.extend({
        'separator': ',' // 同名参数的分隔符，多用于checkbox的值
    })

    var params = this.serializeArray()
    ,   paramMap = {}

    ,   i = 0
    ,   l = params.length
    ,   param;

    for (; i < l; i++) {
        param = params[i];

        if (paramMap[param.name]) {
            paramMap[param.name] += opts.separator + param.value;
        } else {
            paramMap[param.name] = param.value;
        }
    };

    return paramMap;
};
</pre>

善用方法，可以让自己的工作变简单很多。