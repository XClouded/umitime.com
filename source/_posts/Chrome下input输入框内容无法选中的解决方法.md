title: Chrome下input输入框内容无法选中的解决方法
tags:
  - JavaScript
  - js选中input内容
date: 2012-08-21 16:31:09
---

来源于： http://www.cnblogs.com/danye/archive/2012/08/07/2626572.html

首先，我把选中内容放在了onfocus事件中处理，然后FF成功失败交替出现，而chrome则总是失败。采用上述链接中的方法后，解决问题。

解决方法如下;

<pre>
$("input").click(function(e){
　　$(this).select();
});
//阻止chrome自己的mouseup事件即可。
$("input").mouseup(function(e){
    if(window.navigator.userAgent.indexOf("Chrome")!=-1){
        var event = e||window.event; 
        event.preventDefault(); 
    }
});
</pre>

&nbsp;