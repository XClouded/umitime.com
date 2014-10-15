title: JS数组的降序，升序，随机排序
tags:
  - JavaScript
  - 数组排序
date: 2012-08-09 16:19:00
---

<pre>function asc(x, y) {
    return x &gt; y;
};
function desc(x, y) {
    return x &lt; y; 
}; 
function random() {
    return Math.random() &gt; 0.5;
};

var arr = [8,9,6,12,5,2,20];
//升序
console.info(a.sort(asc));
//降序
console.info(a.sort(desc));
//随机
console.info(a.sort(random));</pre>