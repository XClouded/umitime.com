title: 移动端开发的调试工具Weinre
tags:
  - 移动开发
  - Weinre
date: 2013-12-04 03:00:23
---

### Weinre

在移动上面开发调试是很复杂的，所以就有了_weinre_。安装[weinre](http://people.apache.org/~pmuellr/weinre/docs/latest/)可以实现pc来调试手机页面，所以对于移动开发调试是很重要的哦~

![http://people.apache.org/~pmuellr/weinre/docs/latest/images/weinre-demo.jpg](http://people.apache.org/~pmuellr/weinre/docs/latest/images/weinre-demo.jpg)

<span id="more-1027"></span>

### 安装weinre

weinre可以通过npm来安装：

<pre>npm install -g weinre</pre>

安装完之后，可执行下面的命令来启动：

<pre>weinre --httpPort 8080 --boundHost -all-</pre>

这样访问自己的127.0.0.1:8080按照提示在需要调试页面中插入一段js，然后就可以调试了。操作界面类似Chrome的 DevTools，具体操作可以看下[http://people.apache.org/~pmuellr/weinre/docs/latest/Running.html](http://people.apache.org/~pmuellr/weinre/docs/latest/Running.html)教程

### 原理

通过在需要调试的页面中引入一段weinre的js，实现pc和手机的socket通信，从而实现实时调试。

### Tips

1.  如果你嫌每次都要在调试的页面引入js麻烦，可以做个书签或者chrome插件
2.  如果嫌安装麻烦，可以使用phonegap的weinre：[http://debug.phonegap.com/](http://debug.phonegap.com/)

【以上内容节选自：[http://js8.in/2013/11/20/五个你必须知道的javascript和web-debug技术/](http://js8.in/2013/11/20/%E4%BA%94%E4%B8%AA%E4%BD%A0%E5%BF%85%E9%A1%BB%E7%9F%A5%E9%81%93%E7%9A%84javascript%E5%92%8Cweb-debug%E6%8A%80%E6%9C%AF/)】