title: 关于Facebook的页面加载技术BigPipe一点思考
tags:
  - 前端优化
  - BigPipe
  - facebook
  - 加快页面加载
date: 2012-04-17 18:07:01
---

BigPipe的大概思想是将一个页面分块同时异步加载。

对比的说，比如将一个页面分成几块，每一个使用一个ajax请求来加载，可以加速页面载入。这个属于常规手段，应该都明白。但是这样的结果就是造成页面形成翻倍请求。

而BigPipe是反其道而行，浏览器加载一个页面 是一次请求，而这个请求在服务器端分成不同的块，分多线程分别处理某一个块，，然后异步flush返回结果，每个块因为是无序的，通过js来把相应的块填充到相应的位置，不至于页面呈现上的乱序。

简单说前者是先分块后请求，后者是先请求后分块。不过压力都在服务器。

最近很多服务器都实现了comet，而comet应该可以成为BigPipe技术在服务器端的一种实现手段。

引用BigPipe技术简介：

> <span id="more-711"></span> 现在的浏览器，显示网页时需要经历连续的几个步骤，分别是请求网页 -&gt; 服务器端的页面生成 -&gt; 返回全部内容 -&gt; 浏览器渲染，在这一过程中，“服务器的页面生成”到“返回全部内容”阶段，浏览器什么也不做，大部分浏览器就直接显示空白。可想而知，如果页面庞大，那么等待的时间就很长，这很可能导致大量的用户丢失。Facebook 提出的 BigPipe 技术就是为了解决这个问题，它是基于多线程实现，原理大致可以分为以下两点。
> 
> 将一个页面分为多个的 PageLet，每个的 PageLet 实际上就是一个 HTML 片段，每个 PageLet 的页面内容由单独的线程生成与处理。
> 
> 由于使用了多线程，PageLet 内容的返回顺序无法确定，因此如果将内容直接写回 HTML 文档内，它的位置是无法确定的，因此需要借助 JavaScript 将内容插入到正确的位置，因为脚本代码的位置无关紧要。
> 
> 实现了以上两点，最终的效果将是网页中首先出现网页结构和基本的、简单的信息，然后才会在网页的各个 PageLet 位置出现具体内容，这些 PageLet 没有按流模型从上到下从左到右出现，而是“并行出现”，加载页面速度加快。从以上的分析，这种技术至少有两种好处。
> 
> 首先出现的结构和基本信息，告诉用户页面正在加载，是有希望的。
> 
> 并行加载的机制使得某个 PageLet 的缓慢不会影响到别的 PageLet 的加载。
> 
> 所有的 PageLet 在同一个 HTTP 请求内处理。

资料：

http://www.ibm.com/developerworks/cn/java/j-lo-bigpipe/index.html

http://www.cnblogs.com/BearsTaR/archive/2010/06/18/facebook_html_chunk.html

http://codemonkeyism.com/facebook-bigpipe-java/