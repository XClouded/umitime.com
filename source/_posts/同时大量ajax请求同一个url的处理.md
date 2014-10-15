title: 同时大量ajax请求同一个url的处理
tags:
  - JavaScript
  - ajax
date: 2012-08-28 18:58:56
---

情景比如： 一个load数据的按钮，因为网络比较慢或其他原因，用户一直狂点这个按钮，就会发出N多ajax请求出去。因为网络延迟，多个ajax返回的回调处理函数可能会交叉影响，造成意料外的结果。

其中一种方式是，对按钮的点击做限制，某个时间段内不允许重复点击，但是治标不治本，网络差的时候，也会发生意外状况。

这里想到的是一个目前还相对简单的处理方式，使用jQuery的jQuery ajaxPrefilter方法做预处理。普通ajax请求同理也是可以处理的。代码如下：

<pre>
/* ajax请求预前过滤器 */
$.ajaxPrefilter(function(options, originalOptions, jqXHR) {
    var args = originalOptions;
    var _url = args.url || location.href;
    var data = args.data || {};
    //参数名+url做唯一判定
    var xhrId = [_url];
    for(var key in data) {
        xhrId.push(key);
    };
    xhrId = xhrId.join('');

    var cacheXhr = window[xhrId]; 
    if(cacheXhr &#038;&#038; cacheXhr.readyState != 4) {
        cacheXhr.abort();
    };
    window[xhrId] = jqXHR;
})

</pre>

&nbsp;