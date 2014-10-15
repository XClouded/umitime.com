title: JavaScript attribute和property的区别
tags:
  - JavaScript
  - attribute
  - property
date: 2012-08-14 18:47:09
---

前几天还在想了解JavaScript attribute和property的区别，今天就碰到了一篇不错的内容。

补充个特别例子，就是 input的value，用attribute和property设置和获取的完全是不一样的值。

以下内容源自：[http://stylechen.com/attribute-property.html](http://stylechen.com/attribute-property.html "attribute和property的区别")

DOM元素的attribute和property很容易混倄在一起，分不清楚，两者是不同的东西，但是两者又联系紧密。很多新手朋友，也包括以前的我，经常会搞不清楚。

<span id="more-933"></span>

attribute翻译成中文术语为“特性”，property翻译成中文术语为“属性”，从中文的字面意思来看，确实是有点区别了，先来说说attribute。

attribute是一个特性节点，每个DOM元素都有一个对应的attributes属性来存放所有的attribute节点，attributes是一个类数组的容器，说得准确点就是NameNodeMap，总之就是一个类似数组但又和数组不太一样的容器。attributes的每个数字索引以名值对(name=”value”)的形式存放了一个attribute节点。

<pre class="brush: c-sharp"></pre>

&nbsp;

<div id="box" class="box">hello</div>

上面的div元素的HTML代码中有class、id还有自定义的gameid，这些特性都存放在attributes中，类似下面的形式：

<pre class="brush: c-sharp">[ class="box", id="testBox", gameid="880" ]</pre>

可以这样来访问attribute节点：

<pre class="brush: c-sharp">var elem = document.getElementById( 'box' );
console.log( elem.attributes[0].name ); // class
console.log( elem.attributes[0].value ); // box</pre>

但是IE6-7将很多东西都存放在attributes中，上面的访问方法和标准浏览器的返回结果又不同。通常要获取一个attribute节点直接用getAttribute方法：

<pre class="brush: c-sharp">console.log( elem.getAttribute('gameid'); // 880</pre>

要设置一个attribute节点使用setAttribute方法，要删除就用removeAttribute：

<pre class="brush: c-sharp">elem.setAttribute('testAttr', 'testVal');
console.log( elem.removeAttribute('gameid') ); // undefined</pre>

attributes是会随着添加或删除attribute节点动态更新的。

property就是一个属性，如果把DOM元素看成是一个普通的Object对象，那么property就是一个以名值对(name=”value”)的形式存放在Object中的属性。要添加和删除property也简单多了，和普通的对象没啥分别：

<pre class="brush: c-sharp">elem.gameid = 880; // 添加
console.log( elem.gameid ) // 获取
delete elem.gameid // 删除</pre>

之所以attribute和property容易混倄在一起的原因是，很多attribute节点还有一个相对应的property属性，比如上面的div元素的id和class既是attribute，也有对应的property，不管使用哪种方法都可以访问和修改。

<pre class="brush: c-sharp">console.log( elem.getAttribute('id') ); // box
console.log( elem.id ); // box
elem.id = 'hello';
console.log( elem.getAttribute('id') ); // hello</pre>

但是对于自定义的attribute节点，或者自定义property，两者就没有关系了。

<pre class="brush: c-sharp">console.log( elem.getAttribute('gameid') ); // 880
console.log( elem.gameid ); // undefined
elem.areaid = '900';
console.log( elem.getAttribute('areaid') ) // null</pre>

对于IE6-7来说，没有区分attribute和property：

<pre class="brush: c-sharp">console.log( elem.getAttribute('gameid') ); // 880
console.log( elem.gameid ); // 880
elem.areaid = '900';
console.log( elem.getAttribute('areaid') ) // 900</pre>

很多新手朋友估计都很容易掉进这个坑中。

DOM元素一些默认常见的attribute节点都有与之对应的property属性，比较特殊的是一些值为Boolean类型的property，如一些表单元素：

<pre class="brush: c-sharp"><input id="raido" type="radio" checked="checked" />
var radio = document.getElementById( 'radio' );
console.log( radio.getAttribute('checked') ); // checked
console.log( radio.checked ); // true</pre>

对于这些特殊的attribute节点，只有存在该节点，对应的property的值就为true，如：

<pre class="brush: c-sharp"><input id="raido" type="radio" checked="checked" />
var radio = document.getElementById( 'radio' );
console.log( radio.getAttribute('checked') ); // anything
console.log( radio.checked ); // true</pre>

最后为了更好的区分attribute和property，基本可以总结为attribute节点都是在HTML代码中可见的，而property只是一个普通的名值对属性。

<pre class="brush: c-sharp">// gameid和id都是attribute节点
// id同时又可以通过property来访问和修改</pre>
<div id="box">hello</div>

// areaid仅仅是property elem.areaid = 900;