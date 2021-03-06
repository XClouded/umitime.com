title: Node.js使用的场景
tags:
  - JavaScript
  - nodejs
  - nodejs使用场景
date: 2012-05-03 21:15:43
---

工欲善其事必先利其器， 利其器前也要选好这个“器”。Nodejs好像很好，但是也不是什么场景都好用，以下可以做些参考。

**来源于：http://cnodejs.org/topic/4f97d5b8407edba2146030dd**

原文地址在这里：http://nodeguide.com/convincing_the_boss.html 英文版，题目的意思是说服老板的指南，文章中就Node.js应用场景这一重要话题说了一些看法，我摘取其中比较重要的段落，翻译在这里：

<span id="more-748"></span>

### <span style="color: #ff0000;">糟糕的用例：</span>

高CPU消耗的app：

尽管我喜欢node.js，却有很多的用例是不靠谱的。最显而易见的用例是那种CPU使用率高同时I/O操作小的。所以如果你打算写一个视频编码软件，人工智能软件或者类似的CPU使用率高的软件，请不要用node.js。要是你肯稍微变通一下，用C或C++效果会更好。

话虽这么说，node.js允许你很轻松的写C++扩展，所以你可以把它作为一个你核心算法的脚本引擎。

简单的CRUD / HTML apps

尽管可以说node.js是个写各种web程序的不错的工具，你也不该期待它在这点上能比PHP，Ruby或Python带给你更多的好处。是的，你的app也许最终会多一点可扩展性，可是不会因为你是用node.js写的就会神奇般的的带来更多的访问量。

事实是，当我们去看node.js的好的框架时，目前还没有并没有比Rails, CakePHP或者Django更强大的应用场景。如果你的app只是简单的基于一些数据库做HTML的渲染，使用node.js不会给你显而易见的好处。

NoSQL + Node.js + 各种扯淡的时髦词汇

如果你的下一个app的架构读起来像一个NoSQL做配料的菜谱，请花点时间读读下面的内容。

是的，Redis, CouchDB, MongoDB, Riak, Casandra等等确实诱人，但是夏娃的红苹果也一样难以抗拒。如果你准备用node.js来冒一次技术风险，你就不该再加上更多的你没有完全理解的技术。

确实，有一些文档数据库的合理的用例，但是如果你准备在你的软件上建立一个商业环境，坚持那些保守点的数据库技术（像postgres或mysql）也许胜过于你满足你迂腐的技术观，也胜过于只是让你的朋友觉得你很牛。

### <span style="color: #ff0000;">好的用例</span>

JSON API

建立轻量级的REST / JSON api正是node.js的亮点。如果你要包上你的数据库操作或web服务，同时通过JSON把接口暴露出来，node.js的无阻塞模型和JavaScript技术让它成为你非常好的一个选择。

单页的app

如果你打算写一个AJAX操作非常多的单页面app（比如gmail），node.js是非常合适的。在极短的响应时间内处理大量请求的能力，不同的客户端共享像确认信息之类的东西，这些都让node.js成为那种在客户端做很多处理的web程序的很好的选择。

对unix工具的脚本化调用

node.js现在还很年轻，它正在试图为自己重新发明各种软件，不过更好的办法是深入到现有的广阔的命令行工具世界里。Node.js拥有产生数以千计的子进程的能力，同时可以把这些子进程的输出以流的方式处理，这让它成为那种和现有软件寻求平衡时的很好的选择。

流数据

传统的web程序讲http请求和响应作为元事件处理。可事实是它们是流，很多很酷的node.js程序正是利用这个优点创建的。最牛的例子是实时解析上传文件，还在不同的数据层建立了代理。

软件实时程序

node.js另一个很大的方面是你可以很轻松的开发软实时系统。这是指那些像twitter，聊天软件，体彩或实时通讯网络的接口。

但是这里你得注意了，因为JavaScript的动态垃圾回收特性，你得到的响应次数是依赖于你多频繁以及多久触发垃圾回收机制（此时你的程序停止了）。所以不要试图在node里建立硬实时系统，那需要永远一致的响应时间。Erlang在这一类的程序上应该是更好的选择。

最后附上一篇 http://www.infoq.com/cn/news/2012/05/suggest-boss-nodejs，这是Node.js早期核心类库的贡献者Flexi所描述的优劣。