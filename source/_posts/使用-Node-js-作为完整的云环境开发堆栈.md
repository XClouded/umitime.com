title: 使用 Node.js 作为完整的云环境开发堆栈
tags:
  - code
  - solution
date: 2011-08-21 13:02:30
---

<span style="color: #008000;">来源于： http://howe.im/编程/使用-node-js-作为完整的云环境开发堆栈.html</span>

<span style="color: black; font-size: 10pt;"><span style="font-family: 宋体;">随着技术创新表面上继续以指数级速度发展，新思想层出不穷。服务器端的</span><span style="font-family: Arial;"> JavaScript </span><span style="font-family: 宋体;">就是这些新思想之一。</span><span style="font-family: Arial;"> Node.js </span><span style="font-family: 宋体;">是一种事件驱动的</span><span style="font-family: Arial;"> I/O </span><span style="font-family: 宋体;">框架，用于</span><span style="font-family: Arial;"> UNIX </span><span style="font-family: 宋体;">类平台上的</span><span style="font-family: Arial;"> V8 JavaScript </span><span style="font-family: 宋体;">引擎，适合于编写可伸缩的网络程序，如</span><span style="font-family: Arial;"> Web </span><span style="font-family: 宋体;">服务器。</span><span style="font-family: Arial;"> Node.js </span><span style="font-family: 宋体;">正是这种新思想的实现。</span><span style="font-family: Arial;"> </span></span>

<span style="color: black; font-size: 10pt;"><span style="font-family: Arial;">Node.js </span><span style="font-family: 宋体;">并非与</span><span style="font-family: Arial;"> JavaScript </span><span style="font-family: 宋体;">抗衡，而是使用它作为完整的开发堆栈，从服务器端代码一直延伸到浏览器。</span><span style="font-family: Arial;">Node.js </span><span style="font-family: 宋体;">还充分利用了另一种</span><span style="font-family: Arial;"><span style="font-family: 宋体;">创新思想：通过回调利用异步</span><span style="font-family: Arial;"> I/O </span><span style="font-family: 宋体;">的并发性模型。</span></span></span><span style="color: black; font-size: 10pt;"><span style="font-family: Arial;"> </span></span>

<span style="color: black;"><span style="font-size: 16pt;">**<span style="font-family: Arial;">Node.js </span><span style="font-family: 宋体;">云计算平台</span>**</span><span style="font-family: Arial; font-size: 10pt;"> </span></span>

<span style="color: black; font-size: 10pt;"><span style="font-family: 宋体;">在云计算环境中使用</span><span style="font-family: Arial;"> Node.js </span><span style="font-family: 宋体;">框架时，能显示出它的一个巨大优点。对于应用程序开发人员，这往往归结使用平台即服务</span><span style="font-family: Arial;"> (PaaS) </span><span style="font-family: 宋体;">或基础架构即服务</span><span style="font-family: Arial;"> (IaaS) </span><span style="font-family: 宋体;">模型。对于开发人员而言，最抽象和公认最方便的方法是使用</span><span style="font-family: Arial;"> PaaS </span><span style="font-family: 宋体;">提供程序。图</span><span style="font-family: Arial;"> 1 </span><span style="font-family: 宋体;">十分简单地说明了</span><span style="font-family: Arial;"> PaaS </span><span style="font-family: 宋体;">和</span><span style="font-family: Arial;"> IaaS </span><span style="font-family: 宋体;">模型的结构。</span></span>

<span id="more-349"></span>

<span style="color: black;"><span style="font-family: 宋体; font-size: 13pt;"><span style="font-size: 10pt;">**<span style="font-family: 宋体;">图</span><span style="font-family: Arial;"> 1\. PaaS </span><span style="font-family: 宋体;">与</span><span style="font-family: Arial;"> IaaS </span><span style="font-family: 宋体;">结构</span>**</span><span style="font-family: 宋体; font-size: 13pt;">[![](http://www.cssor.com/wp-content/uploads/2011/08/080211_1520_Nodejs1.gif "080211_1520_Nodejs1")](http://www.cssor.com/wp-content/uploads/2011/08/080211_1520_Nodejs1.gif) <span style="font-family: 宋体; font-size: 13pt;"> </span></span></span></span>

&nbsp;

<span style="font-size: 10pt;"><span style="color: black;"><span style="font-family: 宋体;">最近，一个激动人心的开源项目</span><span style="font-family: Arial;"> [<span style="color: #4c6e94; text-decoration: underline;">Cloud Foundry</span>](http://www.cloudfoundry.org/) </span><span style="font-family: 宋体;">公布了代码以创建一个能够运行</span><span style="font-family: Arial;"> Node.js </span><span style="font-family: 宋体;">的私有</span><span style="font-family: Arial;"> PaaS</span><span style="font-family: 宋体;">。</span><span style="font-family: Arial;"><span style="font-family: 宋体;">同样的主机引擎也可用在公共云和商业云中，而且它们接受软件补丁。</span></span></span></span>

<span style="color: black; font-size: 10pt;"><span style="font-family: 宋体;">基础架构管理是一大痛点，如果能够将这项工作外包（永远！）给规模经营的提供商，且无论是源代码，还是物理硬件资源，对于开发人员确实是一个激动人心的时刻。</span><span style="font-family: Arial;"> </span></span>

&nbsp;

<a name="N100CE"></a>

<span style="color: black;"><span style="font-family: 宋体; font-size: 16pt;">**使用**</span><span style="font-family: Arial;"><span style="font-size: 16pt;">** Node.js shell**</span></span></span>

<span style="color: black; font-size: 10pt;"><span style="font-family: 宋体;">在我们着手编写一个完整的</span><span style="font-family: Arial;"> Node.js </span><span style="font-family: 宋体;">例子之前，让我们先开始介绍如何使用交互式</span><span style="font-family: Arial;"> shell</span><span style="font-family: 宋体;">。如果尚未安装</span><span style="font-family: Arial;"> Node.js</span><span style="font-family: 宋体;">，您可以参考资源部分，然后按照说明安装它，或者使用在线的交互式</span><span style="font-family: Arial;"> Node.js </span><span style="font-family: 宋体;">站点之一，它允许您直接在浏览器中输入代码。</span></span>

<span style="color: black;"><span style="font-size: 10pt;"><span style="font-family: 宋体;">要在</span><span style="font-family: Arial;"> Node.js </span><span style="font-family: 宋体;">中以交互方式编写</span><span style="font-family: Arial;"> JavaScript </span><span style="font-family: 宋体;">函数，在命令行提示中输入</span></span><span style="font-family: Courier New; font-size: 12pt;">node</span><span style="font-size: 10pt;"><span style="font-family: 宋体;">，如下所示：</span></span></span>

<div>
<table style="border-collapse: collapse;" border="0">
<colgroup> </colgroup>
<tbody>
<tr>
<td style="padding-top: 2px; padding-left: 2px; padding-bottom: 5px; padding-right: 2px; border: solid #cccccc 0.75pt;" valign="middle"><span style="color: black; font-family: Lucida Console; font-size: 8pt;">lion% node </span></p>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">&gt; var foo = {bar: ‘baz’}; </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">&gt; console.log(foo); </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">{ bar: ‘baz’ } </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">&gt; </span></td>
</tr>
</tbody>
</table>
</div>
<p><span style="color: black;"><span style="font-family: 宋体; font-size: 10pt;">在这个例子中，创建了对象</span><span style="font-family: Courier New; font-size: 12pt;">foo</span><span style="font-family: 宋体; font-size: 10pt;">，然后调用</span><span style="font-family: Courier New; font-size: 12pt;">console.log</span><span style="font-size: 10pt;"><span style="font-family: 宋体;">将它输出到控制台。</span><span style="font-family: Arial;"><span style="font-family: 宋体;">这十分有效而且有趣，不过当您使用</span><span style="font-family: Arial;"> tab </span><span style="font-family: 宋体;">完成功能来探讨</span><span style="font-family: Courier New; font-size: 12pt;">foo</span><span style="font-size: 10pt;"><span style="font-family: 宋体;">时，如下面的例子所示，真正的乐趣才刚刚开始。</span><span style="font-family: Arial;"><span style="font-family: 宋体;">如果输入</span><span style="font-family: Courier New; font-size: 12pt;">foo.bar.</span><span style="font-size: 10pt;"><span style="font-family: 宋体;">，然后按下</span><span style="font-family: Arial;"> tab </span><span style="font-family: 宋体;">键，您将看到对象上的可用方法。</span></span></span></span></span></span></span>

<div>
<table style="border-collapse: collapse;" border="0">
<colgroup> </colgroup>
<tbody>
<tr>
<td style="padding-top: 2px; padding-left: 2px; padding-bottom: 5px; padding-right: 2px; border: solid #cccccc 0.75pt;" valign="middle"><span style="color: black; font-family: Lucida Console; font-size: 8pt;">&gt; foo.bar. </span></p>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">[...output suppressed for space...] </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">foo.bar.toUpperCase foo.bar.trim </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">foo.bar.trimLeft foo.bar.trimRight</span></td>
</tr>
</tbody>
</table>
</div>
<p><span style="color: black;"><span style="font-size: 10pt;"><span style="font-family: 宋体;">试用</span></span><span style="font-family: Courier New; font-size: 12pt;">toUpperCase</span><span style="font-size: 10pt;"><span style="font-family: 宋体;">方法似乎很有趣，下面显示了它的用法：</span></span></span>

<div>
<table style="border-collapse: collapse;" border="0">
<colgroup> </colgroup>
<tbody>
<tr>
<td style="padding-top: 2px; padding-left: 2px; padding-bottom: 5px; padding-right: 2px; border: solid #cccccc 0.75pt;" valign="middle"><span style="color: black; font-family: Lucida Console; font-size: 8pt;"><span style="color: black; font-family: Lucida Console; font-size: 8pt;">&gt; foo.bar.toUpperCase(); </span></span></p>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">‘BAZ’</span></td>
</tr>
</tbody>
</table>
</div>
<p><span style="font-size: 10pt;"><span style="color: black;"><span style="font-family: 宋体;">您可以看到，该方法将字符串转换为大写字母。这类交互式开发非常适合于使用像</span><span style="font-family: Arial;"> Node.js </span><span style="font-family: 宋体;">这样的事件驱动型框架进行开发。</span></span><span style="font-family: Arial;"> </span></span>

<span style="color: black; font-size: 10pt;"><span style="font-family: 宋体;">在完成简单介绍之后，我们开始真正地构建一些东西。</span><span style="font-family: Arial;"> </span></span>

&nbsp;

<a name="N1010D"></a>

<span style="color: black;"><span style="font-size: 16pt;">**<span style="font-family: 宋体;">用</span><span style="font-family: Arial;"> Node.js </span><span style="font-family: 宋体;">构建聊天服务器</span>**</span><span style="font-family: Arial; font-size: 10pt;"> </span></span>

<span style="color: black; font-size: 10pt;"><span style="font-family: Arial;">Node.js </span><span style="font-family: 宋体;">让编写基于事件的网络服务器变得十分简单。例如，让我们创建一些聊天服务器。</span><span style="font-family: Arial;"><span style="font-family: 宋体;">第一个服务器十分简单，几乎没有什么功能，也没有任何异常处理。</span><span style="font-family: Arial;"> </span></span></span>

<span style="color: black; font-size: 10pt;"><span style="font-family: 宋体;">一个聊天服务器允许多个客户端连接到它。每个客户端都可以编写消息，然后广播给所有其他用户。下面给出了最简单的聊天服务器的代码。</span></span>

<div>
<table style="border-collapse: collapse;" border="0">
<colgroup> </colgroup>
<tbody>
<tr>
<td style="padding-top: 2px; padding-left: 2px; padding-bottom: 5px; padding-right: 2px; border: solid #cccccc 0.75pt;" valign="middle"><span style="color: black; font-family: Lucida Console; font-size: 8pt;"><span style="color: black; font-family: Lucida Console; font-size: 8pt;">net = require(‘net’); </span></span></p>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">var sockets = []; </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">var s = net.Server(function(socket) { </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">sockets.push(socket); </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">socket.on(‘data’, function(d) { </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">for (var i=0; i &lt; sockets.length; i++ ) { </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">sockets[i].write(d); </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">} </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">}); </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">}); </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">s.listen(8001);</span></td>
</tr>
</tbody>
</table>
</div>
<p><span style="font-size: 10pt;"><span style="color: black;"><span style="font-family: 宋体;">在不到</span><span style="font-family: Arial;"> 20 </span><span style="font-family: 宋体;">行代码中（实际上，真正实现功能的代码只有</span><span style="font-family: Arial;"> 8 </span><span style="font-family: 宋体;">行），您已经构建了一个能够使用的聊天服务器。</span><span style="font-family: Arial;"><span style="font-family: 宋体;">下面是这个简单程序的流程：</span><span style="font-family: Arial;"> </span></span></span></span>

*   <span style="color: black; font-size: 10pt;"><span style="color: black; font-size: 10pt;"><span style="font-family: 宋体;">当一个套接字进行连接时，将该套接字对象附加到一个数组。</span><span style="font-family: Arial;"> </span></span></span>
*   <span style="color: black; font-size: 10pt;"><span style="color: black; font-size: 10pt;"><span style="font-family: 宋体;">当客户端写入它们的连接时，将该数据写到所有的套接字。</span><span style="font-family: Arial;"> </span></span></span>

<span style="color: black;"><span style="font-size: 10pt;"><span style="font-family: 宋体;">现在，让我们检查所有代码，并解释这个例子如何实现聊天服务器预定功能。第一行允许访问</span></span><span style="font-family: Courier New; font-size: 12pt;">net</span><span style="font-size: 10pt;"><span style="font-family: 宋体;">模块的内容：</span></span></span>

<div>
<table style="border-collapse: collapse;" border="0">
<colgroup> </colgroup>
<tbody>
<tr>
<td style="padding-top: 2px; padding-left: 2px; padding-bottom: 5px; padding-right: 2px; border: solid #cccccc 0.75pt;" valign="middle"><span style="color: black; font-family: Lucida Console; font-size: 8pt;">net = require(‘net’);</span></td>
</tr>
</tbody>
</table>
</div>

<span style="color: black;"><span style="font-size: 10pt;"><span style="font-family: 宋体;">让我们使用这个模块中的</span></span><span style="font-family: Courier New; font-size: 12pt;">Server</span><span style="font-family: 宋体; font-size: 10pt;">。</span></span>

<span style="color: black; font-size: 10pt;"><span style="font-family: 宋体;">您将需要一个位置来保存所有客户端连接，以便在写入数据时可以写到它们中去。</span><span style="font-family: Arial;"><span style="font-family: 宋体;">下面是用于保存所有客户端套接字连接的变量：</span></span></span>

<span style="color: black;"><span style="font-family: Courier New; font-size: 12pt;">var sockets = [];</span></span>

<span style="color: black; font-size: 10pt;"><span style="font-family: 宋体;">下一行开始一个代码块，规定当每个客户端连接时要做的事情。</span></span>

<div>
<table style="border-collapse: collapse;" border="0">
<colgroup> </colgroup>
<tbody>
<tr>
<td style="padding-top: 2px; padding-left: 2px; padding-bottom: 5px; padding-right: 2px; border: solid #cccccc 0.75pt;" valign="middle"><span style="color: black; font-family: Lucida Console; font-size: 8pt;">var s = net.Server(function(socket) {</span></td>
</tr>
</tbody>
</table>
</div>

<span style="color: black;"><span style="font-size: 10pt;"><span style="font-family: 宋体;">传递到</span></span><span style="font-family: Courier New; font-size: 12pt;">Server</span><span style="font-size: 10pt;"><span style="font-family: 宋体;">中的惟一参数是将针对每个客户端连接进行调用的一个函数。</span><span style="font-family: Arial;"><span style="font-family: 宋体;">在这个函数中，将客户端连接添加到所有客户端连接的列表中：</span></span></span></span>

<div>
<table style="border-collapse: collapse;" border="0">
<colgroup> </colgroup>
<tbody>
<tr>
<td style="padding-top: 2px; padding-left: 2px; padding-bottom: 5px; padding-right: 2px; border: solid #cccccc 0.75pt;" valign="middle"><span style="color: black; font-family: Lucida Console; font-size: 8pt;">sockets.push(socket);</span></td>
</tr>
</tbody>
</table>
</div>

<span style="font-size: 10pt;"><span style="color: black; font-family: 宋体;">下一部分代码建立了一个事件处理器，规定了当一个客户端发送数据时要做的事情：</span></span>

<div>
<table style="border-collapse: collapse;" border="0">
<colgroup> </colgroup>
<tbody>
<tr>
<td style="padding-top: 2px; padding-left: 2px; padding-bottom: 5px; padding-right: 2px; border: solid #cccccc 0.75pt;" valign="middle"><span style="color: black; font-family: Lucida Console; font-size: 8pt;">socket.on(‘data’, function(d) { </span></p>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">for (var i=0; i &lt; sockets.length; i++ ) { </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">sockets[i].write(d); </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">} </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">});</span></td>
</tr>
</tbody>
</table>
</div>
<p><span style="color: black;"><span style="font-family: Courier New; font-size: 12pt;">socket.on()</span><span style="font-size: 10pt;"><span style="font-family: 宋体;">方法调用为节点注册一个事件处理器，以便当某些事件发生时它知道如何处理。</span><span style="font-family: Arial;"><span style="font-family: 宋体;">当接收到来自客户端的数据时，</span><span style="font-family: Arial;">Node.js </span><span style="font-family: 宋体;">会调用这个特殊的事件处理器。其他的事件处理器包括</span><span style="font-family: Courier New; font-size: 12pt;">connect</span><span style="font-family: 宋体; font-size: 10pt;">、</span><span style="font-family: Courier New; font-size: 12pt;">end</span><span style="font-family: 宋体; font-size: 10pt;">、</span><span style="font-family: Courier New; font-size: 12pt;">timeout</span><span style="font-family: 宋体; font-size: 10pt;">、</span><span style="font-family: Courier New; font-size: 12pt;">drain</span><span style="font-family: 宋体; font-size: 10pt;">、</span><span style="font-family: Courier New; font-size: 12pt;">error</span><span style="font-size: 10pt;"><span style="font-family: 宋体;">和</span></span><span style="font-family: Courier New; font-size: 12pt;">close</span><span style="font-family: 宋体; font-size: 10pt;">。</span></span></span></span>

<span style="color: black;"><span style="font-family: Courier New; font-size: 12pt;">socket.on()</span><span style="font-size: 10pt;"><span style="font-family: 宋体;">方法调用的结构类似于前面提过的</span></span><span style="font-family: Courier New; font-size: 12pt;">Server()</span><span style="font-size: 10pt;"><span style="font-family: 宋体;">调用。</span><span style="font-family: Arial;"><span style="font-family: 宋体;">您传入一个函数给这两者，当有事发生时调用此函数。这种回调方法在异步网络框架中很常见。</span><span style="font-family: Arial;"><span style="font-family: 宋体;">这是当开始使用像</span><span style="font-family: Arial;"> Node.js </span><span style="font-family: 宋体;">这样的异步框架时，拥有过程编程经验的人会遇到的主要问题。</span></span></span></span></span>

<span style="color: black; font-size: 10pt;"><span style="font-family: 宋体;">在这种情况下，当任意客户端发送数据给服务器时，就会调用这个匿名函数并将数据传入函数中。它基于您已经积累的套接字对象列表进行迭代，</span><span style="font-family: Arial;"><span style="font-family: 宋体;">并给它们全部发送相同的数据。每个客户端连接都将接收到这些数据。</span></span></span>

<span style="color: black; font-size: 10pt;"><span style="font-family: 宋体;">这个聊天服务器十分简单，它缺少一些非常基础的功能，比如识别是谁发送哪条消息，或者处理某个客户端断开的情况。</span><span style="font-family: Arial;"><span style="font-family: 宋体;">（如果一个客户端从这台聊天服务器断开，任何人发送消息，服务器都会崩溃。）</span></span></span>

<span style="color: black; font-size: 10pt;"><span style="font-family: 宋体;">下面的源代码（在下载示例文件中叫做</span><span style="font-family: Arial;"> chat2.js </span><span style="font-family: 宋体;">）是一个经过改进的套接字服务器，其功能有所增强，能够处理</span><span style="font-family: Arial;">“</span><span style="font-family: 宋体;">糟糕的情况</span><span style="font-family: Arial;">“</span><span style="font-family: 宋体;">（比如客户端断开）。</span></span>

<div>
<table style="border-collapse: collapse;" border="0">
<colgroup> </colgroup>
<tbody>
<tr>
<td style="padding-top: 2px; padding-left: 2px; padding-bottom: 5px; padding-right: 2px; border: solid #cccccc 0.75pt;" valign="middle"><span style="color: black; font-family: Lucida Console; font-size: 8pt;">net = require(‘net’); </span></p>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">var sockets = []; </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">var name_map = new Array(); </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">var chuck_quotes = [ </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">"There used to be a street named after Chuck Norris, but it was changed because </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">nobody crosses Chuck Norris and lives.", </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">"Chuck Norris died 20 years ago, Death just hasn't built up the courage to tell </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">him yet.", </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">"Chuck Norris has already been to Mars; that's why there are no signs of life.", </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">"Some magicians can walk on water, Chuck Norris can swim through land.", </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">"Chuck Norris and Superman once fought each other on a bet. The loser had to start </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">wearing his underwear on the outside of his pants." </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">] </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">function get_username(socket) { </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">var name = socket.remoteAddress; </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">for (var k in name_map) { </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">if (name_map[k] == socket) { </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">name = k; </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">} </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">} </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">return name; </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">} </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">function delete_user(socket) { </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">var old_name = get_username(socket); </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">if (old_name != null) { </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">delete(name_map[old_name]); </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">} </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">} </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">function send_to_all(message, from_socket, ignore_header) { </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">username = get_username(from_socket); </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">for (var i=0; i &lt; sockets.length; i++ ) { </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">if (from_socket != sockets[i]) { </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">if (ignore_header) { </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">send_to_socket(sockets[i], message); </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">} </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">else { </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">send_to_socket(sockets[i], username + ‘: ‘ + message); </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">} </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">} </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">} </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">} </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">function send_to_socket(socket, message) { </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">socket.write(message + ‘\n’); </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">} </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">function execute_command(socket, command, args) { </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">if (command == ‘identify’) { </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">delete_user(socket); </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">name = args.split(‘ ‘, 1)[0]; </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">name_map[name] = socket; </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">} </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">if (command == ‘me’) { </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">name = get_username(socket); </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">send_to_all(‘**’ + name + ‘** ‘ + args, socket, true); </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">} </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">if (command == ‘chuck’) { </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">var i = Math.floor(Math.random() * chuck_quotes.length); </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">send_to_all(chuck_quotes[i], socket, true); </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">} </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">if (command == ‘who’) { </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">send_to_socket(socket, ‘Identified users:’); </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">for (var name in name_map) { </span>

s<span style="color: black; font-family: Lucida Console; font-size: 8pt;">end_to_socket(socket, ‘- ‘ + name); </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">} </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">} </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">} </span>

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">function send_private_message(socket, recipient_name, message) { </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> to_socket = name_map[recipient_name]; </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> if (! to_socket) { </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> send_to_socket(socket, recipient_name + ‘ is not a valid user’); </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> return; </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> } </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> send_to_socket(to_socket, ‘[ DM ' + get_username(socket) + ' ]: ‘ + message); </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">} </span>

&nbsp;

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">var s = net.Server(function(socket) { </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> sockets.push(socket); </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> socket.on(‘data’, function(d) { </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> data = d.toString(‘utf8′).trim(); </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> // check if it is a command </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> var cmd_re = /^\/([a-z]+)[ ]*(.*)/g; </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> var dm_re = /^@([a-z]+)[ ]+(.*)/g; </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> cmd_match = cmd_re.exec(data) </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> dm_match = dm_re.exec(data) </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> if (cmd_match) { </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> var command = cmd_match[1]; </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> var args = cmd_match[2]; </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> execute_command(socket, command, args); </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> } </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> // check if it is a direct message </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> else if (dm_match) { </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> var recipient = dm_match[1]; </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> var message = dm_match[2]; </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> send_private_message(socket, recipient, message); </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> } </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> // if none of the above, send to all </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> else { </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> send_to_all(data, socket); </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> }; </span>

&nbsp;

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> }); </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> socket.on(‘close’, function() { </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> sockets.splice(sockets.indexOf(socket), 1); </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> delete_user(socket); </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> }); </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">}); </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">s.listen(8001);</span></td>
</tr>
</tbody>
</table>
</div>
<p>&nbsp;

<a name="N1019A"></a>

<span style="color: black;"><span style="font-family: 宋体; font-size: 16pt;">**稍微高级一点的主题：聊天服务器的负载平衡**</span><span style="font-family: Arial; font-size: 10pt;"> </span></span>

&nbsp;

<span style="color: black; font-size: 10pt;"><span style="font-family: 宋体;">通常，负载按比例增长也是部署到云环境的理由之一。这种部署需要实现一些负载平衡机制。</span><span style="font-family: Arial;"> </span></span>

&nbsp;

<span style="color: black; font-size: 10pt;"><span style="font-family: 宋体;">大多数轻量级</span><span style="font-family: Arial;"> Web </span><span style="font-family: 宋体;">服务器，比如</span><span style="font-family: Arial;"> nginx </span><span style="font-family: 宋体;">和</span><span style="font-family: Arial;"> lighttpd</span><span style="font-family: 宋体;">，都能够针对多台</span><span style="font-family: Arial;"> HTTP </span><span style="font-family: 宋体;">服务器进行负载平衡，但如果您想要在非</span><span style="font-family: Arial;"> HTTP </span><span style="font-family: 宋体;">服务器之间实现平衡，</span><span style="font-family: Arial;">nginx </span><span style="font-family: 宋体;">可能无法满足要求。而且尽管存在通用的</span><span style="font-family: Arial;"> TCP </span><span style="font-family: 宋体;">负载平衡器，您可能不会喜欢它们使用的负载平衡算法。或者它们没有提供您想要使用的一些功能。或者，您只是想享受构造自己的负载平衡器的乐趣。</span><span style="font-family: Arial;"> </span></span>

&nbsp;

<span style="color: black; font-size: 10pt;"><span style="font-family: 宋体;">下面是最简单的负载平衡器。它没有实现任何故障恢复，希望所有的目的地都是可用的，而且没有进行任何错误处理。它十分简约。</span><span style="font-family: Arial;"><span style="font-family: 宋体;">基本的理念是，它接收一个来自客户端的套接字连接，随机挑选一个目标服务器进行连接，然后将来自客户端的所有数据转发给该服务器，并将来自该服务器的所有数据都发回到客户端。</span><span style="font-family: Arial;"> </span></span></span>

&nbsp;

&nbsp;

<div>
<table style="border-collapse: collapse;" border="0">
<colgroup>
<col style="width: 582px;" /> </colgroup>
<tbody>
<tr>
<td style="padding-top: 2px; padding-left: 2px; padding-bottom: 5px; padding-right: 2px; border: solid #cccccc 0.75pt;" valign="middle"><span style="color: black; font-family: Lucida Console; font-size: 8pt;">net = require(‘net’); </span></p>

&nbsp;

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">var destinations = [ </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> ['localhost', 8001], </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> ['localhost', 8002], </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> ['localhost', 8003], </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">] </span>

&nbsp;

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">var s = net.Server(function(client_socket) { </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> var i = Math.floor(Math.random() * destinations.length); </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> console.log(“connecting to ” + destinations[i].toString() + “\n”); </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> var dest_socket = net.Socket(); </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> dest_socket.connect(destinations[i][1], destinations[i][0]); </span>

&nbsp;

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> dest_socket.on(‘data’, function(d) { </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> client_socket.write(d); </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> }); </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> client_socket.on(‘data’, function(d) { </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> dest_socket.write(d); </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> }); </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">}); </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">s.listen(9001);</span></td>
</tr>
</tbody>
</table>
</div>
<p>&nbsp;

<span style="color: black;"><span style="font-family: Courier New; font-size: 12pt;">destinations</span><span style="font-size: 10pt;"><span style="font-family: 宋体;">的定义是我们要进行平衡的后端服务器的配置。</span><span style="font-family: Arial;"><span style="font-family: 宋体;">这是一个简单的多维数组，主机名是第一个元素，端口号是第二个元素。</span><span style="font-family: Arial; font-size: 10pt;"> </span></span></span></span>

&nbsp;

&nbsp;

<span style="color: black;"><span style="font-family: Courier New; font-size: 12pt;">Server()</span><span style="font-size: 10pt;"><span style="font-family: 宋体;">的定义类似于聊天服务器的例子。您创建一个套接字服务器，并让它监听一个端口。这次它将监听</span><span style="font-family: Arial;"> 9001 </span><span style="font-family: 宋体;">端口。</span><span style="font-family: Arial;"> </span></span></span>

&nbsp;

<span style="color: black;"><span style="font-size: 10pt;"><span style="font-family: 宋体;">针对</span></span><span style="font-family: Courier New; font-size: 12pt;">Server()</span><span style="font-size: 10pt;"><span style="font-family: 宋体;">定义的回调首先随机选择一个要连接到的目的地：</span><span style="font-family: Arial;"> </span></span></span>

&nbsp;

<span style="color: black;"><span style="font-family: Courier New; font-size: 12pt;">var i = Math.floor(Math.random() * destinations.length);</span><span style="font-family: Arial; font-size: 10pt;"> </span></span>

&nbsp;

<span style="color: black; font-size: 10pt;"><span style="font-family: 宋体;">您可能已经使用过轮询算法或使用</span><span style="font-family: Arial;">“</span><span style="font-family: 宋体;">最少连接数</span><span style="font-family: Arial;">“</span><span style="font-family: 宋体;">算法完成一些额外的工作然后离去，但我们想尽可能地保持简单。</span><span style="font-family: Arial;"> </span></span>

&nbsp;

<span style="color: black;"><span style="font-size: 10pt;"><span style="font-family: 宋体;">这个例子中有两个指定的套接字对象：</span></span><span style="font-family: Courier New; font-size: 12pt;">client_socket</span><span style="font-size: 10pt;"><span style="font-family: 宋体;">和</span></span><span style="font-family: Courier New; font-size: 12pt;">dest_socket</span><span style="font-size: 10pt;"><span style="font-family: 宋体;">。</span><span style="font-family: Arial;"> </span></span></span>

&nbsp;

*   <span style="color: black;"><span style="color: black;"><span style="font-family: Courier New; font-size: 12pt;">client_socket</span><span style="font-size: 10pt;"><span style="font-family: 宋体;">是负载平衡器与客户端之间的连接。</span><span style="font-family: Arial;"> </span></span></span></span>
*   <span style="color: black;"><span style="color: black;"><span style="font-family: Courier New; font-size: 12pt;">dest_socket</span><span style="font-size: 10pt;"><span style="font-family: 宋体;">是负载平衡器与被平衡服务器之间的连接。</span><span style="font-family: Arial;"> </span></span></span></span>

<span style="color: black; font-size: 10pt;"><span style="font-family: 宋体;">这两个套接字分别处理一个事件：接收到的数据。当它们其中一个收到数据时，就会将数据写到另一个套接字。</span><span style="font-family: Arial;"> </span></span>

&nbsp;

<span style="color: black; font-size: 10pt;"><span style="font-family: 宋体;">让我们完整地了解当一个客户端通过负载平衡器连接到通用网络服务器上，发送数据，然后接收数据时发生的事情。</span><span style="font-family: Arial;"> </span></span>

&nbsp;

1.  <span style="color: black;"><span style="color: black;"><span style="font-size: 10pt;"><span style="font-family: 宋体;">当一个客户的连接到负载平衡器时，</span><span style="font-family: Arial;">Node.js </span><span style="font-family: 宋体;">在客户端与自己本身之间创建一个套接字，我们称之为</span></span><span style="font-family: Courier New; font-size: 12pt;">client_socket</span><span style="font-size: 10pt;"><span style="font-family: 宋体;">。</span><span style="font-family: Arial;"> </span></span></span></span>
2.  <span style="color: black;"><span style="color: black;"><span style="font-size: 10pt;"><span style="font-family: 宋体;">当连接建立之后，负载平衡器挑选一个目的地并创建一个指向该目的地的套接字连接，我们称之为</span></span><span style="font-family: Courier New; font-size: 12pt;">dest_socket</span><span style="font-size: 10pt;"><span style="font-family: 宋体;">。</span><span style="font-family: Arial;"> </span></span></span></span>
3.  <span style="color: black; font-size: 10pt;"><span style="color: black; font-size: 10pt;"><span style="font-family: 宋体;">当客户端发送数据时，负载平衡器将相同的数据推送到目的地服务器。</span><span style="font-family: Arial;"> </span></span></span>
4.  <span style="color: black;"><span style="color: black;"><span style="font-size: 10pt;"><span style="font-family: 宋体;">当目的地服务器做出响应并将一些数据写到</span></span><span style="font-family: Courier New; font-size: 12pt;">dest_socket</span><span style="font-size: 10pt;"><span style="font-family: 宋体;">时，负载平衡器通过</span></span><span style="font-family: Courier New; font-size: 12pt;">client_socket</span><span style="font-size: 10pt;"><span style="font-family: 宋体;">将这些数据推送回客户端。</span><span style="font-family: Arial;"> </span></span></span></span>

<span style="color: black; font-size: 10pt;"><span style="font-family: 宋体;">可以对这个负载平衡器进行一些改进，包括错误处理，在同一个进程中嵌入另一个进程以动态增加和移除目的地，增加不同的平衡算法，以及增加一些容错处理。</span><span style="font-family: Arial;"> </span></span>

&nbsp;

<a name="N1020F"></a>

<span style="color: black;"><span style="font-size: 16pt;">**<span style="font-family: 宋体;">超越原生解决方案：</span><span style="font-family: Arial;">Express Web </span><span style="font-family: 宋体;">框架</span>**</span><span style="font-family: Arial; font-size: 10pt;"> </span></span>

&nbsp;

<span style="color: black; font-size: 10pt;"><span style="font-family: Arial;">Node.js </span><span style="font-family: 宋体;">配备有</span><span style="font-family: Arial;"> HTTP </span><span style="font-family: 宋体;">服务器功能，但较为低级。如果要在</span><span style="font-family: Arial;"> Node.js </span><span style="font-family: 宋体;">中构建一个</span><span style="font-family: Arial;"> Web </span><span style="font-family: 宋体;">应用程序，您可能会考虑</span><span style="font-family: Arial;"> [<span style="color: #4c6e94; text-decoration: underline;">Express</span>](http://expressjs.com/)——</span><span style="font-family: 宋体;">一个为</span><span style="font-family: Arial;"> Node.js </span><span style="font-family: 宋体;">打造的</span><span style="font-family: Arial;"> Web </span><span style="font-family: 宋体;">应用程序开发框架。它弥补了</span><span style="font-family: Arial;"> Node.js </span><span style="font-family: 宋体;">的一些不足。</span><span style="font-family: Arial;"> </span></span>

&nbsp;

<span style="color: black; font-size: 10pt;"><span style="font-family: 宋体;">在下一个例子中，让我们重点关注使用</span><span style="font-family: Arial;"> Express </span><span style="font-family: 宋体;">胜过简单的</span><span style="font-family: Arial;"> Node.js </span><span style="font-family: 宋体;">的一些明显优势。</span><span style="font-family: Arial;"><span style="font-family: 宋体;">请求路由就是其中之一，还有一个是为</span><span style="font-family: Arial;"> HTTP “verb” </span><span style="font-family: 宋体;">类型注册一个事件，比如</span><span style="font-family: Arial;">“get”</span><span style="font-family: 宋体;">或</span><span style="font-family: Arial;">“post”</span><span style="font-family: 宋体;">。</span><span style="font-family: Arial;"> </span></span></span>

&nbsp;

&nbsp;

<span style="color: black; font-size: 10pt;"><span style="font-family: 宋体;">下面给出了一个十分简单的</span><span style="font-family: Arial;"> Web </span><span style="font-family: 宋体;">应用程序，它只是演示了</span><span style="font-family: Arial;"> Express </span><span style="font-family: 宋体;">的一些基本功能。</span><span style="font-family: Arial;"> </span></span>

&nbsp;

<div>
<table style="border-collapse: collapse;" border="0">
<colgroup>
<col style="width: 582px;" /> </colgroup>
<tbody>
<tr>
<td style="padding-top: 2px; padding-left: 2px; padding-bottom: 5px; padding-right: 2px; border: solid #cccccc 0.75pt;" valign="middle"><span style="color: black; font-family: Lucida Console; font-size: 8pt;">var app = require(‘express’).createServer(); </span></p>

&nbsp;

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">app.get(‘/’, function(req, res){ </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> res.send(‘This is the root.’); </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">}); </span>

&nbsp;

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">app.get(‘/root/:id’, function(req, res){ </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> res.send(‘You sent ‘ + req.params.id + ‘ as an id’); </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">}); </span>

&nbsp;

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">app.listen(7000);</span></td>
</tr>
</tbody>
</table>
</div>
<p>&nbsp;

<span style="color: black;"><span style="font-size: 10pt;"><span style="font-family: 宋体;">这两行以</span></span><span style="font-family: Courier New; font-size: 12pt;">app.get()</span><span style="font-size: 10pt;"><span style="font-family: 宋体;">开始的代码是事件处理器，当</span><span style="font-family: Arial;"> GET </span><span style="font-family: 宋体;">请求进入时就会触发。</span><span style="font-family: Arial;"><span style="font-family: 宋体;">这两次方法调用的第一个参数是一个正则表达式，用于指定用户可能传入的</span><span style="font-family: Arial;"> URL</span><span style="font-family: 宋体;">。第二个参数是真正处理请求的一个函数。</span><span style="font-family: Arial; font-size: 10pt;"> </span></span></span></span>

&nbsp;

&nbsp;

<span style="color: black;"><span style="font-size: 10pt;"><span style="font-family: 宋体;">正则表达式参数是路由机制。如果请求类型（</span><span style="font-family: Arial;">GET</span><span style="font-family: 宋体;">、</span><span style="font-family: Arial;">POST</span><span style="font-family: 宋体;">等）与资源（</span><span style="font-family: Arial;">/, /root/123</span><span style="font-family: 宋体;">）匹配，就会调用处理器函数。在第一次</span></span><span style="font-family: Courier New; font-size: 12pt;">app.get()</span><span style="font-size: 10pt;"><span style="font-family: 宋体;">调用中，</span></span><span style="font-family: Courier New; font-size: 12pt;">/</span><span style="font-size: 10pt;"><span style="font-family: 宋体;">被简单地指定为资源。而在第二次调用中，在指定</span></span><span style="font-family: Courier New; font-size: 12pt;">/root</span><span style="font-size: 10pt;"><span style="font-family: 宋体;">时后面还加了一个</span><span style="font-family: Arial;"> ID</span><span style="font-family: 宋体;">。映射</span><span style="font-family: Arial;"> regex </span><span style="font-family: 宋体;">的</span><span style="font-family: Arial;"> URL </span><span style="font-family: 宋体;">中资源前面的冒号</span><span style="font-family: Arial;">(</span></span><span style="font-family: Courier New; font-size: 12pt;">:</span><span style="font-size: 10pt;"><span style="font-family: Arial;">) </span><span style="font-family: 宋体;">字符表明，这部分稍后可作为一个参数使用。</span><span style="font-family: Arial;"> </span></span></span>

&nbsp;

<span style="color: black;"><span style="font-size: 10pt;"><span style="font-family: 宋体;">当请求类型与正规表达式匹配时，就会调用处理器函数。</span><span style="font-family: Arial;"><span style="font-family: 宋体;">此函数带有两个参数，一个请求（</span><span style="font-family: Courier New; font-size: 12pt;">req</span><span style="font-size: 10pt;"><span style="font-family: 宋体;">）</span><span style="font-family: Arial;"><span style="font-family: 宋体;">和一个响应（</span><span style="font-family: Courier New; font-size: 12pt;">res</span><span style="font-size: 10pt;"><span style="font-family: 宋体;">）。</span><span style="font-family: Arial;"><span style="font-family: 宋体;">前面提到的参数被附加给请求对象。而</span><span style="font-family: Arial;"> Web </span><span style="font-family: 宋体;">服务器传回给用户的消息被传入到响应对象。</span><span style="font-family: Arial;"> </span></span></span></span></span></span></span></span>

&nbsp;

&nbsp;

&nbsp;

&nbsp;

<span style="color: black; font-size: 10pt;"><span style="font-family: 宋体;">这是一个非常简单的例子，但已经清楚地说明</span><span style="font-family: Arial;">“</span><span style="font-family: 宋体;">真正的应用程序</span><span style="font-family: Arial;">“</span><span style="font-family: 宋体;">如何利用这个框架来构建更加丰富和完整的功能。如果插入一个模板系统和一些数据引擎（传统的或</span><span style="font-family: Arial;"> NoSQL </span><span style="font-family: 宋体;">均可），您可以轻松构建出一组功能来满足真正应用程序的需求。</span><span style="font-family: Arial;"> </span></span>

&nbsp;

<span style="color: black; font-size: 10pt;"><span style="font-family: Arial;">Express </span><span style="font-family: 宋体;">的特点之一是高性能。这与其他快速</span><span style="font-family: Arial;"> Web </span><span style="font-family: 宋体;">应用程序框架的常见特性一起，让</span><span style="font-family: Arial;"> Express </span><span style="font-family: 宋体;">在注重高性能和海量可伸缩性的云部署领域中占据了重要的位置。</span><span style="font-family: Arial;"> </span></span>

&nbsp;

<a name="N10252"></a>

<span style="color: black;"><span style="font-family: 宋体; font-size: 16pt;">**应了解的知识**</span><span style="font-family: Arial; font-size: 10pt;"> </span></span>

&nbsp;

<span style="color: black; font-size: 10pt;"><span style="font-family: 宋体;">有两个概念</span><span style="font-family: Arial;">/</span><span style="font-family: 宋体;">趋势需要了解：</span><span style="font-family: Arial;"> </span></span>

&nbsp;

*   <span style="color: black; font-size: 10pt;"><span style="color: black; font-size: 10pt;"><span style="font-family: 宋体;">键</span><span style="font-family: Arial;">/</span><span style="font-family: 宋体;">值数据库的突然流行。</span><span style="font-family: Arial;"> </span></span></span>
*   <span style="color: black; font-size: 10pt;"><span style="color: black; font-size: 10pt;"><span style="font-family: 宋体;">其他异步的</span><span style="font-family: Arial;"> Web </span><span style="font-family: 宋体;">范型。</span><span style="font-family: Arial;"> </span></span></span>

<span style="color: black;"><span style="font-size: 12pt;">**<span style="font-family: 宋体;"><a name="N10264"></a>键</span><span style="font-family: Arial;">/</span><span style="font-family: 宋体;">值数据库</span><span style="font-family: Arial;">… </span><span style="font-family: 宋体;">为什么突然流行？</span>**</span><span style="font-family: Arial; font-size: 10pt;"> </span></span>

&nbsp;

<span style="color: black; font-size: 10pt;"><span style="font-family: 宋体;">因为</span><span style="font-family: Arial;"> JavaScript </span><span style="font-family: 宋体;">是</span><span style="font-family: Arial;"> Web </span><span style="font-family: 宋体;">的通用语言，对于</span><span style="font-family: Arial;"> JavaScript Object Notation (JSON) </span><span style="font-family: 宋体;">的讨论通常远远落后于</span><span style="font-family: Arial;"> JavaScript </span><span style="font-family: 宋体;">相关的研究。</span><span style="font-family: Arial;"> JSON </span><span style="font-family: 宋体;">是在</span><span style="font-family: Arial;"> JavaScript </span><span style="font-family: 宋体;">与一些其他语言之间交换数据的最常用途径。</span><span style="font-family: Arial;">JSON </span><span style="font-family: 宋体;">本质上是一种键</span><span style="font-family: Arial;">/</span><span style="font-family: 宋体;">值存储，因此天生适用于对键</span><span style="font-family: Arial;">/</span><span style="font-family: 宋体;">值数据库感兴趣的</span><span style="font-family: Arial;"> JavaScript </span><span style="font-family: 宋体;">和</span><span style="font-family: Arial;"> Node.js </span><span style="font-family: 宋体;">开发人员。毕竟，如果能够以</span><span style="font-family: Arial;"> JSON </span><span style="font-family: 宋体;">格式存储数据，</span><span style="font-family: Arial;">JavaScript </span><span style="font-family: 宋体;">开发人员的工作就将变得轻松很多。</span><span style="font-family: Arial;"> </span></span>

&nbsp;

<span style="color: black; font-size: 10pt;"><span style="font-family: 宋体;">有一个不太相关的趋势，在</span><span style="font-family: Arial;"> NoSQL </span><span style="font-family: 宋体;">数据库环境中也会涉及键</span><span style="font-family: Arial;">/</span><span style="font-family: 宋体;">值数据库。</span><span style="font-family: Arial;">CAP </span><span style="font-family: 宋体;">定理（也叫做</span><span style="font-family: Arial;"> Brewer </span><span style="font-family: 宋体;">定理）指出，一个分布式系统有</span><span style="font-family: Arial;"> 3 </span><span style="font-family: 宋体;">个核心属性：</span><span style="font-family: Arial;"><span style="font-family: 宋体;">一致性、可用性和分区容忍性（</span><span style="color: #4c6e94;"><span style="font-family: Arial; text-decoration: underline;">formal proof of CAP</span><span style="color: black;"><span style="font-family: 宋体;">）。</span><span style="font-family: Arial;"><span style="font-family: 宋体;">这条定理是</span><span style="font-family: Arial;"> NoSQL </span><span style="font-family: 宋体;">发展背后的推动力量，它为牺牲传统关系数据库的某些特性以换取（通常是高可用性）提供了理论基础。一些流行的键</span><span style="font-family: Arial;">/</span><span style="font-family: 宋体;">值数据库</span><span style="font-family: Arial;"><span style="font-family: 宋体;">包括</span><span style="font-family: Arial;"> Riak</span><span style="font-family: 宋体;">、</span><span style="font-family: Arial;">Cassandra</span><span style="font-family: 宋体;">、</span><span style="font-family: Arial;">CouchDB </span><span style="font-family: 宋体;">和</span><span style="font-family: Arial;"> MongoDB</span><span style="font-family: 宋体;">。</span><span style="font-family: Arial;"> </span></span></span></span></span></span></span>

&nbsp;

&nbsp;

&nbsp;

&nbsp;

<span style="color: black;"><span style="font-size: 12pt;">**<span style="font-family: 宋体;"><a name="N10273"></a>异步</span><span style="font-family: Arial;"> Web </span><span style="font-family: 宋体;">范型</span>**</span><span style="font-family: Arial; font-size: 10pt;"> </span></span>

&nbsp;

<span style="color: black; font-size: 10pt;"><span style="font-family: 宋体;">事件驱动的异步</span><span style="font-family: Arial;"> Web </span><span style="font-family: 宋体;">框架已经存在了相当长一段时间。其中最流行和最新的异步</span><span style="font-family: Arial;"> Web </span><span style="font-family: 宋体;">框架是</span><span style="font-family: Arial;"> Tornado</span><span style="font-family: 宋体;">，它使用</span><span style="font-family: Arial;"> Python </span><span style="font-family: 宋体;">语言编写，在</span><span style="font-family: Arial;"> Facebook </span><span style="font-family: 宋体;">内部使用。</span><span style="font-family: Arial;"><span style="font-family: 宋体;">下面这个例子说明了</span><span style="font-family: Arial;"> hello_world </span><span style="font-family: 宋体;">在</span><span style="font-family: Arial;"> Tornado </span><span style="font-family: 宋体;">中（在下载示例文件中叫做</span><span style="font-family: Arial;"> hello_tornado.py </span><span style="font-family: 宋体;">）是什么样子。</span><span style="font-family: Arial;"> </span></span></span>

&nbsp;

&nbsp;

<div>
<table style="border-collapse: collapse;" border="0">
<colgroup>
<col style="width: 582px;" /> </colgroup>
<tbody>
<tr>
<td style="padding-top: 2px; padding-left: 2px; padding-bottom: 5px; padding-right: 2px; border: solid #cccccc 0.75pt;" valign="middle"><span style="color: black; font-family: Lucida Console; font-size: 8pt;">import tornado.ioloop </span></p>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">import tornado.web </span>

&nbsp;

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">class MainHandler(tornado.web.RequestHandler): </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> def get(self): </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> self.write(“Hello, world”) </span>

&nbsp;

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">application = tornado.web.Application([ </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> (r"/", MainHandler), </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">]) </span>

&nbsp;

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;">if __name__ == “__main__”: </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> application.listen(8888) </span>

&nbsp;

<span style="color: black; font-family: Lucida Console; font-size: 8pt;"> tornado.ioloop.IOLoop.instance().start()</span></td>
</tr>
</tbody>
</table>
</div>
<p>&nbsp;

<span style="font-size: 10pt;"><span style="color: black;"><span style="font-family: Arial;">Twisted.web </span><span style="font-family: 宋体;">也是用</span><span style="font-family: Arial;"> Python </span><span style="font-family: 宋体;">语言写的，工作方式也十分类似。</span></span><span style="font-family: Arial;"> </span></span>

&nbsp;

<span style="color: black; font-size: 10pt;"><span style="font-family: 宋体;">最后谈到真正的</span><span style="font-family: Arial;"> Web </span><span style="font-family: 宋体;">服务器本身，与</span><span style="font-family: Arial;"> Apache </span><span style="font-family: 宋体;">不同，</span><span style="font-family: Arial;">nginx </span><span style="font-family: 宋体;">不使用线程，而是使用一种事件驱动的（异步）架构来处理请求。</span><span style="font-family: Arial;"><span style="font-family: 宋体;">异步</span><span style="font-family: Arial;"> Web </span><span style="font-family: 宋体;">框架使用</span><span style="font-family: Arial;"> nginx </span><span style="font-family: 宋体;">作为其</span><span style="font-family: Arial;"> Web </span><span style="font-family: 宋体;">服务器是十分常见的情况。</span><span style="font-family: Arial;"> </span></span></span>

&nbsp;

&nbsp;

<a name="N10286"></a>

<span style="color: black;"><span style="font-family: 宋体; font-size: 16pt;">**结束语**</span><span style="font-family: Arial; font-size: 10pt;"> </span></span>

&nbsp;

<span style="color: black; font-size: 10pt;"><span style="font-family: Arial;">Node.js </span><span style="font-family: 宋体;">在</span><span style="font-family: Arial;"> Web </span><span style="font-family: 宋体;">开发人员中非常引人关注。它允许开发团队同时在客户端和服务器端上编写</span><span style="font-family: Arial;"> JavaScript</span><span style="font-family: 宋体;">。</span><span style="font-family: Arial;"><span style="font-family: 宋体;">它们还可以结合与</span><span style="font-family: Arial;"> JavaScript </span><span style="font-family: 宋体;">相关的强大技术：</span><span style="font-family: Arial;">JQuery</span><span style="font-family: 宋体;">、</span><span style="font-family: Arial;">V8</span><span style="font-family: 宋体;">、</span><span style="font-family: Arial;">JSON </span><span style="font-family: 宋体;">和事件驱动的编程。</span><span style="font-family: Arial;"><span style="font-family: 宋体;">另外还有基于</span><span style="font-family: Arial;"> Node.js </span><span style="font-family: 宋体;">开发的生态系统，比如</span><span style="font-family: Arial;"> Express Web </span><span style="font-family: 宋体;">框架。</span><span style="font-family: Arial;"> </span></span></span></span>

&nbsp;

&nbsp;

&nbsp;

<span style="color: black; font-size: 10pt;"><span style="font-family: Arial;">Node.js </span><span style="font-family: 宋体;">的优点引人关注，它也存在一些缺点。如果是</span><span style="font-family: Arial;"> CPU </span><span style="font-family: 宋体;">密集型编程，就无法体现</span><span style="font-family: Arial;"> Node.js </span><span style="font-family: 宋体;">提供的非阻塞</span><span style="font-family: Arial;"> I/O </span><span style="font-family: 宋体;">方面的优点。</span><span style="font-family: Arial;"><span style="font-family: 宋体;">有些架构可以解决这类问题，比如将一个池中的进程分流到每个</span><span style="font-family: Arial;"> Node.js </span><span style="font-family: 宋体;">实例上运行，但需要由开发人员去实现它。</span><span style="font-family: Arial;"> </span></span></span>

&nbsp;

&nbsp;

&nbsp;

&nbsp;

原文地址: [http://www.ibm.com/developerworks/cloud/library/cl-nodejscloud/index.html](http://www.ibm.com/developerworks/cloud/library/cl-nodejscloud/index.html)