title: ToFishes系列jQuery插件 – jCuteForm美化的表单插件
tags:
  - jQuery
  - jCuteForm
  - jquery plugin
  - jQuery表单美化
  - 表单美化插件
date: 2010-08-25 04:19:35
---

jCuteForm插件，针对单选、多选、下拉框的一个替代美化插件。jCuteForm实际上是3个插件的组合，可以分别拿出来使用，它们是： jRadio, jCheckbox, jSelect。

而且不同于其他一些美化插件的工作原理，是对现有的&lt;radio /&gt;,&lt;select /&gt;等标签进行拆解，内容再封装，生成div模拟的方式，外观定制有些繁琐，而且比较难调控。本插件跳过这几步，依旧遵循ToFishes系列插件结构，表现，行为相分离的原则，结构自定（提供的Demo使用em，a等标签，只是需要一些class等给插件提供参考点，即使是这些class也可以自定），外观由前端人员根据结构自定预先写好，之后调用jCuteForm实现功能。

[![jCuteForm表单美化插件预览图](http://demo.cssor.com/jcuteform/screenshot.png "jCuteForm表单美化插件预览图")](http://demo.cssor.com/jcuteform/)

<span id="more-107"></span>

### <span style="color: #003300;">1、单选及多选框的美化</span>

准备：单选与多选至少有两种状态：选中和未选中，所以需要两个表示不同状态下的图片作为背景。也可以扩展出鼠标悬浮时，鼠标点下时等状态，这时就需要多几个图片了。

<span style="color: #003300;">HTML：</span>

<pre class="brush:xml">&lt;em class="jradio"&gt;&lt;!--单选--&gt;
    &lt;a class="radio  checked" name="初级"&gt;初级&lt;/a&gt;
    &lt;a class="radio" name="高级"&gt;高级&lt;/a&gt;
    &lt;input class="jvalue" type="hidden" name="" value="" /&gt;
&lt;/em&gt;

&lt;em class="jcheckbox"&gt;&lt;!--多选--&gt;
    &lt;input class="jvalue" type="hidden" name="" value="" /&gt;
    &lt;a class="checkbox" name="all-color" /&gt;全部&lt;/a&gt;
    &lt;a class="checkbox" name="red" /&gt;红色&lt;/a&gt;
    &lt;a class="checkbox" name="green" /&gt;绿色&lt;/a&gt;
    &lt;a class="checkbox" name="blue" /&gt;蓝色&lt;/a&gt;
&lt;/em&gt;</pre>

基本原则就是先定义一个圈（比如em，也可以是其他适合的任意标签），在这个圈内提供点选元素（比如a），一个值挂载的input标签。上面的class都是可选配，可自配的，调用插件时指定就行了。

<span style="color: #003300;">css:</span>

<pre class="brush:css">/*jradio模拟radio的样式,jcheckbox模拟checkbox样式*/
.jradio .radio, .jradio .checked, .jcheckbox .checkbox, .jcheckbox .checked {display:inline-block; margin-right:15px; padding-left:15px;
		background:url(img/form-ico.png) no-repeat 0 8px; vertical-align:middle;}
    .jradio .checked {background-position: 0 -50px; }
    .jcheckbox .checkbox {padding-left:18px;background-position: 0 -114px; }
    .jcheckbox .checked { padding-left:18px;background-position: 0 -181px;}</pre>

背景图就像这样：

![单选多选背景图示例图](http://demo.cssor.com/jcuteform/img/form-ico.png "单选多选背景图示例") 之所以间距看起来比较大，主要是因为工作中遇到单选的内容是图片，比较大导致背景图被显示了多个，不过更好的方案是单独的图片定义上下center，这样不管内容有多高，都能按钮居中。

<span style="color: #003300;">jCuteForm使用：</span>

<pre class="brush:js">$(".jradio").jRadio({
    item: ".radio", //单选项元素，默认为此dom元素内部的class为radio的元素。
    input:".jvalue", //挂载值的表单元素，默认为此dom元素内部的&lt;input&gt;元素,可以是任何表单元素，一般为隐藏文本域
    attr: "name", //指定item单选项的值存放在哪个属性中，比如 &lt;a class="radio" name="value" /&gt;, 其值存储在name属性上
    checked: "checked", //被选中元素的class定义,同时作为初始化时要寻找的值
    onchange:function(value){
        alert(value);当前选中的值
    }
});
$(".jcheckbox").jCheckbox({
    item: ".checkbox", //多选项元素，默认为此dom元素内部的class为checkbox的元素。
    input:".jvalue", //挂载值的表单元素，多个值以逗号相隔，默认为此dom元素内部的&lt;input&gt;元素,可以是任何表单元素，一般为隐藏文本域
    attr: "name", //指定item单选项的值存放在哪个属性中，比如 &lt;a class="checkbox" name="value" /&gt;, 其值存储在name属性上
    checked: "checked", //被选中元素的class定义,同时作为初始化时要寻找的值
    onchange:function(value){
         alert(value);//被改变的值
    }
});</pre>

### <span style="color: #003300;">2、下拉框的美化</span>

这个结构比较复杂一些。我选择了div作为外围元素，同时float:left，这样保持一行，而不是换行。

<span style="color: #003300;">HTML：</span>

<pre class="brush:xml">&lt;div class="jselect meta"&gt;
    &lt;p class="jtext"&gt;&lt;input class="jvalue" type="hidden" name="" value="" /&gt;&lt;em&gt;&lt;/em&gt;&lt;/p&gt;&lt;!--显示区域--&gt;
    &lt;div class="db-con"&gt;&lt;!--下拉内容--&gt;
        &lt;ul&gt;
              &lt;li class="option checked hide" name="请选择分类"&gt;--请选择分类--&lt;/li&gt;
              &lt;li class="option" name="我的设计夹"&gt;我的设计夹&lt;/li&gt;
              &lt;li class="option" name="我的摄影集"&gt;我的摄影集&lt;/li&gt;
              &lt;li class="option" name="我的杂物架"&gt;我的杂物架&lt;/li&gt;
        &lt;/ul&gt;
    &lt;/div&gt;
&lt;/div&gt;</pre>

<span style="color: #003300;">参考CSS：</span>

<pre class="brush:css">/* jselect模拟select的样式 */
.meta {float:left;display:inline;}
.jselect { position:relative; margin:0 10px 0 0; height:25px;
        text-align:left;font-size:12px;}
    .jtext {padding:0 0 0 10px;width:150px;height:24px;line-height:24px;
            border:1px solid #ccc;background:url(img/form-input.png) no-repeat;}
    .option {margin:0;height:20px;line-height:20px;}
/* 下拉内容及三角形 */
.jselect em {top:10px; right:10px;line-height:0; border-style:solid;
        border-width:5px 5px 0; border-color:#555 #fff; }
.jselect.curr em { border-width:0 5px 5px !important; border-width:0 6px 6px; }
.db-con, .jselect em { position:absolute;}
.db-con {top:25px; left:0; display:none; padding:2px;
        border:1px solid #ddd; background:#fff; z-index:22; }
    .db-con li { padding:2px 10px; cursor:default;}
    .db-con li.checked{ background:#ff6; }
    .db-con li:hover{ background:#cc6; }</pre>

<span style="color: #003300;">jCuteForm使用：</span>

<pre class="brush:js">$(".jselect").jSelect({
    con:".db-con",
    attr: "name", //这里不使用默认值，因为li的value属性只能被获取到数值类型
    width: -1, //同显示区同宽
    widthFix:6, //这里修正6像素宽度
    onchange: function(value,index){
        preview.html("下拉第" + index + "项被选中，当前选中值： " + value);
    }
});

/* 完整的参数配置列表及说明 */
{
    item: ".option", //相当于select的option元素，下拉的选择项目，默认为此dom元素内部的class为option的元素。
    attr: "value", //指定item选项的值存放在哪个属性中，比如 &lt;li value="value" /&gt;, 其值存储在value属性上(value只能存储数值)
    input: "input", //挂载值的表单元素，多个值以逗号相隔，默认为此dom元素内部的&lt;input&gt;元素,可以是任何表单元素，一般为隐藏文本域
    checked: "checked", //被选中状态下item选项元素的class定义 
    event: "click", //下拉的触发事件，默认单击展开下拉

    show: ".jtext", //用于显示所选择的项目的元素，同时也是点击此处显示下拉内容文字的元素。默认为此dom元素内class为jtext的元素。
    con: ".jdcon", //存储下拉的内容的元素
    width: 0, // 设置下啦内容的宽度，0表示自适应宽度, -1表示同show等宽，大于0的数值则会被设置此数值宽度
    widthFix: 0, //设置宽度增减量，用于与show等宽时的宽度误差
    curr: "curr", // 显示下拉内容时给本元素设置一个当前的样式类class定义，比如用于设置下拉箭头的指向

    onchange: function(v, index){} //当值发生改变时执行某个函数动作,当前值及项目索引作为参数传入该函数
}</pre>

[点击这里查看Demo及下载](http://demo.cssor.com/jcuteform/ "jcuteform表单美化插件演示及下载")