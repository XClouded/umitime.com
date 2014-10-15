title: JS判断委派的事件源
tags:
  - JavaScript
  - event.srcElement
  - event.target
  - js事件源
date: 2012-07-31 12:43:57
---

事件委派有利于性能提升，其中各浏览器兼容的获取事件源的可以如下写：

<pre>/* 获得事件源dom对象 */
function getTarget(e) {
    e = e || window.event;
    var el = e.target || e.srcElement;
    return el;
};

/* 判断事件源是某个标签 */
function isTag(el, tagName) {
    return el.parentNode.tagName.toLowerCase() == tagName;
};</pre>

在stack overflow看到一个解释，关于ie下的事件：

> The problem is that in IE, the event object is not sent as an argument of the handler, it is just a global property (window.event)
> 
> 在ie下有个问题，event对象不会作为参数传递，而是全局window对象下的属性。

地址：http://stackoverflow.com/questions/2642095/access-event-target-in-ie8-unobstrusive-javascript