title: 研究Ajax请求受登录保护的URL的优雅解决
tags:
  - jQuery
  - solution
  - ajax登录
  - jQuery ajajx
date: 2011-11-09 21:33:05
---

在此先记下思路。

场景：登录页面login，在一个登录后才能访问的页面dashboard，dashboard上有一个按钮button，button绑定了一个单击事件，触发后发出一个ajax请求，ajax请求返回一个html片段，并把这个html片段更新到某个dom标签。

条件：dashboard页面如果登录过期，再次访问dashboard或点击button发出ajax请求应该跳转到登录页面login。

问题： 如果对ajax熟悉或有以上类似情况经验的，应该知道，登录过期后，如果不刷新dashboard页面，而直接点击button发出ajax请求，那么ajax获取到的响应内容会是整个登录页面login，而不是正确的那个html片段，这样更新到dom标签的话，页面整个乱掉了。

解决：ajax请求的地址在响应内容前先做登录判断，如过期则返回一个标志位，比如login=false\true，在ajax回调的方法中判断此标志位， 如果login=true，则location.href=登陆页面url。

优雅：理论上是这样，还没试验。拿javaEE应用+jQuery Ajax（版本1.5+）来说。因为我比较熟悉JEE。JEE可以通过过滤器Filter来控制所有的受登录保护的url请求。

jQuery Ajax可以通过ajaxSetup设置全局选项，其中有一项是关于响应的状态码的，在1.5+版本中叫 statusCode ，而且jQuery Ajax有一个请求头：X-Requested-With = XMLHttpRequest

没错了，通过状态码及请求头就可以更方便，更自动的解决登录保护问题。

做法为： Filter通过request.getHeader(“X-Requested-With”)可以判断是否为Ajax请求（所以自己写的ajax最好也加上这个请求头）。 当是Ajax请求并且登录已经过期，则直接在Filter中响应一个状态码401，或者其他自定义各种码。在ajaxSetup全局选项中配置如何处理这个状态码，亦即location.href=登陆页面url。这样就可以统一自动的处理各种登录保护下的ajax请求了。

ajaxSetup例子如下：

<pre class="brush:js">$.ajaxSetup({
  statusCode: {
    '401': function() {
      //alert('登录超时了哦...');
      //如果做了登录自动跳回，可以再加个东东
      var currUrl = encodeURIComponent(location.href);
      location.href = '/login?url=' + currUrl;
    }
  }
});

/* URI编码及解码方法 */
/* 推荐方式 */
console.info(encodeURIComponent('http://www.aa.com/hh ll/'));
console.info(decodeURIComponent('http://www.aa.com/hh ll/'));
/* 另外的简单方式
encodeURI 不对下列字符进行编码：“:”、“/”、“;”和“?”。请使用 encodeURIComponent 对这些字符进行编码
 */
console.info(encodeURI('http://www.aa.com/hh ll/'));
console.info(decodeURI('http://www.aa.com/hh ll/'));</pre>