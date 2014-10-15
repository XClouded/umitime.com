title: Go 和 Python Web 服务器性能对比
tags:
  - 拓展
date: 2011-08-17 17:14:05
---

来源于： http://www.oschina.net/question/25940_19172。之所以转载，文中提到的python web架构是我所感兴趣的，和有价值的。

我通常使用 [Python](http://www.python.org/) 来构建 Web 应用。一年前，在兴趣的驱使下，我开始学习 [Go](http://golang.org/)。 在此期间，我重写了一些原本由 C 开发的 CGI 应用，包括运行于 [chroot](http://en.wikipedia.org/wiki/Chroot) 环境下的同 [thttpd](http://www.acme.com/software/thttpd/) 服务器一起的应用。我开始寻找可以开发易于 chroot、且内置 Web 服务器的独立 Web 应用的工具。那时，我开始玩 [web.go](http://www.getwebgo.com/) 框架、[mustache.go](https://github.com/hoisie/mustache.go) 模板、Go 原生 [http](http://golang.org/pkg/http/) 包和 [GoMySQL](https://github.com/Philio/GoMySQL) 数据库 API。我发现，有 http、mustache.go  GoMySQL 包的 Go 可以是我用来工作的不错的工具组合。因此，我决定使用 Go 编写我的应用。

<span id="more-341"></span>

在工作过程中发现，我需要比 mustache.go 更加灵活，比 GoMySQL 更加成熟、没有那么多 Bug 的东西。最终，我使用 [Kasia.go](https://github.com/ziutek/kasia.go) 模板和 [MyMySQL](https://github.com/ziutek/mymysql) （为我的应用定制开发的包，不过我将其贡献给了 Go 社区）。重写的应用即便是在比以前的负载更高的运营环境下，也工作得很好。我开始思考这个问题：用 Go 实现独立 Web 应用比 Python 到底快了（或者是慢了）多少。我决定做一些各种框架和服务器不同的用途的测试。为了比较，我选择了下面的 Go 包：

*   原始的 Go http包；
*   web.go 框架（它使用运行于独立模式[standalone mode] 的 http 包）；
*   [twister](http://garyburd.github.com/twister/) 框架 （它同样使用 http 包）。

和下面的 Python Web服务器/框架：

*   使用 [CherryPy](http://www.cherrypy.org/) WSGI 服务器的[ web.py](http://webpy.org/) 框架；
*   使用 [flup](http://trac.saddi.com/flup) [FastCGI](http://en.wikipedia.org/wiki/FastCGI) 做 [nginx](http://wiki.nginx.org/Main) 服务器的后台处理的 web.py 框架；
*   [tornado](http://www.tornadoweb.org/) 异步服务器/框架；
*   nginx 做负载均衡的 tornado。

每一个用例，我都编写了一个小应用，略微复杂一些的、传统的 Hello World 例子。任何应用都包括：

*   使用正则表达式通过 URL 路径传递参数；
*   使用语句创建多行输出；
*   使用 printf 形式的格式化函数/表达式格式化输出。

我想，这些都是在 Web 应用中常见的操作，所以应当包含在任何简易的性能对比测试中。所有测试应用的代码在下面的链接中：

*   [Go http](https://github.com/ziutek/web_bench/blob/master/http.go)
*   [web.go](https://github.com/ziutek/web_bench/blob/master/webgo.go)
*   [twister](https://github.com/ziutek/web_bench/blob/master/twister.go)
*   [web.py](https://github.com/ziutek/web_bench/blob/master/webpy.py)
*   [tornado](https://github.com/ziutek/web_bench/blob/master/trnado.py)

## 测试环境

测试环境包括两台 使用千兆以太网链接的PC （请求发起者和应用服务器）。

*   请求发起者：2 x Xeon 2.6 GHz with hyperthreading, Debian SID, kernel: 2.6.33.7.2-rt30-1-686 #1 SMP PREEMPT RT；
*   服务器: MSI Netbook with two core Intel U4100 1.30GHz, AC power connected, 64-bit Ubuntu 10.10, kernel: 2.6.35-25-generic #44-Ubuntu SMP, Python 2.6.6-2ubuntu2, web.py 0.34-2, flup 1.0.2-1, tornado 0.2-1, nginx 0.7.67-3ubuntu1；

为了产生 HTTP 请求并且评估测试应用的性能，我使用 [siege](http://www.joedog.org/index/siege-home) 性能测试工具。Siege 可以用多线程模拟多个用户。我使用了下面的命令产生请求：

&nbsp;

<table border="0" cellspacing="0" cellpadding="0">
<tbody style="outline-width: 0px !important; outline-style: initial !important; outline-color: initial !important; font-size: 1em !important; border-top-left-radius: 0px 0px !important; border-top-right-radius: 0px 0px !important; border-bottom-right-radius: 0px 0px !important; border-bottom-left-radius: 0px 0px !important; background-image: none !important; background-attachment: initial !important; background-origin: initial !important; background-clip: initial !important; background-color: initial !important; bottom: auto !important; float: none !important; height: auto !important; left: auto !important; line-height: 1.1em !important; overflow-x: visible !important; overflow-y: visible !important; position: static !important; right: auto !important; text-align: left !important; top: auto !important; vertical-align: baseline !important; width: auto !important; box-sizing: content-box !important; font-family: Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important; font-weight: normal !important; font-style: normal !important; direction: ltr !important; background-position: initial initial !important; background-repeat: initial initial !important; padding: 0px !important; margin: 0px !important; border: 0px !important initial !important initial !important;">
<tr style="outline-width: 0px !important; outline-style: initial !important; outline-color: initial !important; font-size: 1em !important; border-top-left-radius: 0px 0px !important; border-top-right-radius: 0px 0px !important; border-bottom-right-radius: 0px 0px !important; border-bottom-left-radius: 0px 0px !important; background-image: none !important; background-attachment: initial !important; background-origin: initial !important; background-clip: initial !important; background-color: initial !important; bottom: auto !important; float: none !important; height: auto !important; left: auto !important; line-height: 1.1em !important; overflow-x: visible !important; overflow-y: visible !important; position: static !important; right: auto !important; text-align: left !important; top: auto !important; vertical-align: baseline !important; width: auto !important; box-sizing: content-box !important; font-family: Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important; font-weight: normal !important; font-style: normal !important; direction: ltr !important; background-position: initial initial !important; background-repeat: initial initial !important; padding: 0px !important; margin: 0px !important; border: 0px !important initial !important initial !important;">
<td>
<div class="container" style="outline-width: 0px !important; outline-style: initial !important; outline-color: initial !important; font-size: 1em !important; border-top-left-radius: 0px 0px !important; border-top-right-radius: 0px 0px !important; border-bottom-right-radius: 0px 0px !important; border-bottom-left-radius: 0px 0px !important; background-image: none !important; background-attachment: initial !important; background-origin: initial !important; background-clip: initial !important; background-color: initial !important; bottom: auto !important; float: none !important; height: auto !important; left: auto !important; line-height: 1.1em !important; overflow-x: visible !important; overflow-y: visible !important; position: relative !important; right: auto !important; text-align: left !important; top: auto !important; vertical-align: baseline !important; width: auto !important; box-sizing: content-box !important; font-family: Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important; font-weight: normal !important; font-style: normal !important; direction: ltr !important; background-position: initial initial !important; background-repeat: initial initial !important; padding: 0px !important; margin: 0px !important; border: 0px !important initial !important initial !important;">
<div class="line number1 index0 alt2" style="padding-top: 0px !important; padding-right: 1em !important; padding-bottom: 0px !important; padding-left: 0em !important; outline-width: 0px !important; outline-style: initial !important; outline-color: initial !important; font-size: 1em !important; border-top-left-radius: 0px 0px !important; border-top-right-radius: 0px 0px !important; border-bottom-right-radius: 0px 0px !important; border-bottom-left-radius: 0px 0px !important; background-image: none !important; background-attachment: initial !important; background-origin: initial !important; background-clip: initial !important; background-color: #0a2b1d !important; bottom: auto !important; float: none !important; height: auto !important; left: auto !important; line-height: 1.1em !important; overflow-x: visible !important; overflow-y: visible !important; position: static !important; right: auto !important; text-align: left !important; top: auto !important; vertical-align: baseline !important; width: auto !important; box-sizing: content-box !important; font-family: Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important; font-weight: normal !important; font-style: normal !important; direction: ltr !important; white-space: pre !important; background-position: initial initial !important; background-repeat: initial initial !important; margin: 0px !important; border: 0px !important initial !important initial !important;">`siege -c 200 -t 20s http:`

`//SERVER_ADDR`

`:8080`

`/Hello/100`</div>
</div>
</td>
</tr>
</tbody>
</table>

&nbsp;

或者多个类似的命令，减少参数 -c 的量（在这个测试中，我同时运行了多个 Python 脚本）。它模拟了 200 用户的请求，并持续 20 秒。这个 URL 使得 Web 应用对每个请求都输出 100 行。Go 应用使用 Go 发布版 2011-02-01.1。

## 结果

GOMAXPROCS=1, 一个 Python 进程：

<table>
<tbody style="outline-width: 0px; outline-style: initial; outline-color: initial; font-size: 14px; padding: 0px; margin: 0px; border: 0px initial initial;">
<tr style="outline-width: 0px; outline-style: initial; outline-color: initial; font-size: 14px; padding: 0px; margin: 0px; border: 0px initial initial;">
<th>框架</th>
<th colspan="2">请求速率 [1/sec]</th>
</tr>
<tr style="outline-width: 0px; outline-style: initial; outline-color: initial; font-size: 14px; padding: 0px; margin: 0px; border: 0px initial initial;">
<td>Go http</td>
<td>1350</td>
</tr>
<tr style="outline-width: 0px; outline-style: initial; outline-color: initial; font-size: 14px; padding: 0px; margin: 0px; border: 0px initial initial;">
<td>Twister</td>
<td>1324</td>
</tr>
<tr style="outline-width: 0px; outline-style: initial; outline-color: initial; font-size: 14px; padding: 0px; margin: 0px; border: 0px initial initial;">
<td>Web.go</td>
<td>1141</td>
</tr>
<tr style="outline-width: 0px; outline-style: initial; outline-color: initial; font-size: 14px; padding: 0px; margin: 0px; border: 0px initial initial;">
<td>Tornado</td>
<td>882</td>
</tr>
<tr style="outline-width: 0px; outline-style: initial; outline-color: initial; font-size: 14px; padding: 0px; margin: 0px; border: 0px initial initial;">
<td>Tornado+nginx</td>
<td>862</td>
</tr>
<tr style="outline-width: 0px; outline-style: initial; outline-color: initial; font-size: 14px; padding: 0px; margin: 0px; border: 0px initial initial;">
<td>Web.py+CheryPy</td>
<td>169</td>
</tr>
<tr style="outline-width: 0px; outline-style: initial; outline-color: initial; font-size: 14px; padding: 0px; margin: 0px; border: 0px initial initial;">
<td>Web.py+nginx</td>
<td>114</td>
</tr>
</tbody>
</table>

GOMAXPROCS=2, 两个 Python 并发进程：

<table>
<tbody style="outline-width: 0px; outline-style: initial; outline-color: initial; font-size: 14px; padding: 0px; margin: 0px; border: 0px initial initial;">
<tr style="outline-width: 0px; outline-style: initial; outline-color: initial; font-size: 14px; padding: 0px; margin: 0px; border: 0px initial initial;">
<th>框架</th>
<th colspan="2">请求速率 [1/sec]</th>
</tr>
<tr style="outline-width: 0px; outline-style: initial; outline-color: initial; font-size: 14px; padding: 0px; margin: 0px; border: 0px initial initial;">
<td>Go http</td>
<td>1768</td>
</tr>
<tr style="outline-width: 0px; outline-style: initial; outline-color: initial; font-size: 14px; padding: 0px; margin: 0px; border: 0px initial initial;">
<td>Twister</td>
<td>1746</td>
</tr>
<tr style="outline-width: 0px; outline-style: initial; outline-color: initial; font-size: 14px; padding: 0px; margin: 0px; border: 0px initial initial;">
<td>Tornado</td>
<td>1682</td>
</tr>
<tr style="outline-width: 0px; outline-style: initial; outline-color: initial; font-size: 14px; padding: 0px; margin: 0px; border: 0px initial initial;">
<td>Web.go</td>
<td>1516</td>
</tr>
<tr style="outline-width: 0px; outline-style: initial; outline-color: initial; font-size: 14px; padding: 0px; margin: 0px; border: 0px initial initial;">
<td>Tornado+nginx</td>
<td>1378</td>
</tr>
<tr style="outline-width: 0px; outline-style: initial; outline-color: initial; font-size: 14px; padding: 0px; margin: 0px; border: 0px initial initial;">
<td>Web.py+CheryPy</td>
<td>308</td>
</tr>
<tr style="outline-width: 0px; outline-style: initial; outline-color: initial; font-size: 14px; padding: 0px; margin: 0px; border: 0px initial initial;">
<td>Web.py+nginx</td>
<td>198</td>
</tr>
</tbody>
</table>

GOMAXPROCS=4, 四个 Python 并发进程：

<table>
<tbody style="outline-width: 0px; outline-style: initial; outline-color: initial; font-size: 14px; padding: 0px; margin: 0px; border: 0px initial initial;">
<tr style="outline-width: 0px; outline-style: initial; outline-color: initial; font-size: 14px; padding: 0px; margin: 0px; border: 0px initial initial;">
<th>框架</th>
<th colspan="2">请求速率 [1/sec]</th>
</tr>
<tr style="outline-width: 0px; outline-style: initial; outline-color: initial; font-size: 14px; padding: 0px; margin: 0px; border: 0px initial initial;">
<td>Go http</td>
<td>2063</td>
</tr>
<tr style="outline-width: 0px; outline-style: initial; outline-color: initial; font-size: 14px; padding: 0px; margin: 0px; border: 0px initial initial;">
<td>Twister</td>
<td>2020</td>
</tr>
<tr style="outline-width: 0px; outline-style: initial; outline-color: initial; font-size: 14px; padding: 0px; margin: 0px; border: 0px initial initial;">
<td>Web.go</td>
<td>1753</td>
</tr>
<tr style="outline-width: 0px; outline-style: initial; outline-color: initial; font-size: 14px; padding: 0px; margin: 0px; border: 0px initial initial;">
<td>Tornado</td>
<td>1662</td>
</tr>
<tr style="outline-width: 0px; outline-style: initial; outline-color: initial; font-size: 14px; padding: 0px; margin: 0px; border: 0px initial initial;">
<td>Tornado+nginx</td>
<td>1364</td>
</tr>
<tr style="outline-width: 0px; outline-style: initial; outline-color: initial; font-size: 14px; padding: 0px; margin: 0px; border: 0px initial initial;">
<td>Web.py+CheryPy</td>
<td>304</td>
</tr>
<tr style="outline-width: 0px; outline-style: initial; outline-color: initial; font-size: 14px; padding: 0px; margin: 0px; border: 0px initial initial;">
<td>Web.py+nginx</td>
<td>211</td>
</tr>
</tbody>
</table>

Web.py+nginx 工作的 flup FastCGI 选项：multiplexed=False, multithreaded=False。如果 multiplexed=True 它会运行得慢一些。如果 multithreaded=True 而只有一个进程服务于 nginx 服务器，会报下面的错误：

<table border="0" cellspacing="0" cellpadding="0">
<tbody style="outline-width: 0px !important; outline-style: initial !important; outline-color: initial !important; font-size: 1em !important; border-top-left-radius: 0px 0px !important; border-top-right-radius: 0px 0px !important; border-bottom-right-radius: 0px 0px !important; border-bottom-left-radius: 0px 0px !important; background-image: none !important; background-attachment: initial !important; background-origin: initial !important; background-clip: initial !important; background-color: initial !important; bottom: auto !important; float: none !important; height: auto !important; left: auto !important; line-height: 1.1em !important; overflow-x: visible !important; overflow-y: visible !important; position: static !important; right: auto !important; text-align: left !important; top: auto !important; vertical-align: baseline !important; width: auto !important; box-sizing: content-box !important; font-family: Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important; font-weight: normal !important; font-style: normal !important; direction: ltr !important; background-position: initial initial !important; background-repeat: initial initial !important; padding: 0px !important; margin: 0px !important; border: 0px !important initial !important initial !important;">
<tr style="outline-width: 0px !important; outline-style: initial !important; outline-color: initial !important; font-size: 1em !important; border-top-left-radius: 0px 0px !important; border-top-right-radius: 0px 0px !important; border-bottom-right-radius: 0px 0px !important; border-bottom-left-radius: 0px 0px !important; background-image: none !important; background-attachment: initial !important; background-origin: initial !important; background-clip: initial !important; background-color: initial !important; bottom: auto !important; float: none !important; height: auto !important; left: auto !important; line-height: 1.1em !important; overflow-x: visible !important; overflow-y: visible !important; position: static !important; right: auto !important; text-align: left !important; top: auto !important; vertical-align: baseline !important; width: auto !important; box-sizing: content-box !important; font-family: Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important; font-weight: normal !important; font-style: normal !important; direction: ltr !important; background-position: initial initial !important; background-repeat: initial initial !important; padding: 0px !important; margin: 0px !important; border: 0px !important initial !important initial !important;">
<td>
<div class="container" style="outline-width: 0px !important; outline-style: initial !important; outline-color: initial !important; font-size: 1em !important; border-top-left-radius: 0px 0px !important; border-top-right-radius: 0px 0px !important; border-bottom-right-radius: 0px 0px !important; border-bottom-left-radius: 0px 0px !important; background-image: none !important; background-attachment: initial !important; background-origin: initial !important; background-clip: initial !important; background-color: initial !important; bottom: auto !important; float: none !important; height: auto !important; left: auto !important; line-height: 1.1em !important; overflow-x: visible !important; overflow-y: visible !important; position: relative !important; right: auto !important; text-align: left !important; top: auto !important; vertical-align: baseline !important; width: auto !important; box-sizing: content-box !important; font-family: Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important; font-weight: normal !important; font-style: normal !important; direction: ltr !important; background-position: initial initial !important; background-repeat: initial initial !important; padding: 0px !important; margin: 0px !important; border: 0px !important initial !important initial !important;">
<div class="line number1 index0 alt2" style="padding-top: 0px !important; padding-right: 1em !important; padding-bottom: 0px !important; padding-left: 0em !important; outline-width: 0px !important; outline-style: initial !important; outline-color: initial !important; font-size: 1em !important; border-top-left-radius: 0px 0px !important; border-top-right-radius: 0px 0px !important; border-bottom-right-radius: 0px 0px !important; border-bottom-left-radius: 0px 0px !important; background-image: none !important; background-attachment: initial !important; background-origin: initial !important; background-clip: initial !important; background-color: #0a2b1d !important; bottom: auto !important; float: none !important; height: auto !important; left: auto !important; line-height: 1.1em !important; overflow-x: visible !important; overflow-y: visible !important; position: static !important; right: auto !important; text-align: left !important; top: auto !important; vertical-align: baseline !important; width: auto !important; box-sizing: content-box !important; font-family: Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important; font-weight: normal !important; font-style: normal !important; direction: ltr !important; white-space: pre !important; background-position: initial initial !important; background-repeat: initial initial !important; margin: 0px !important; border: 0px !important initial !important initial !important;">`[error] 18166`

`#0: *66139 connect() to`</div>
<div class="line number2 index1 alt1" style="padding-top: 0px !important; padding-right: 1em !important; padding-bottom: 0px !important; padding-left: 0em !important; outline-width: 0px !important; outline-style: initial !important; outline-color: initial !important; font-size: 1em !important; border-top-left-radius: 0px 0px !important; border-top-right-radius: 0px 0px !important; border-bottom-right-radius: 0px 0px !important; border-bottom-left-radius: 0px 0px !important; background-image: none !important; background-attachment: initial !important; background-origin: initial !important; background-clip: initial !important; background-color: #0a2b1d !important; bottom: auto !important; float: none !important; height: auto !important; left: auto !important; line-height: 1.1em !important; overflow-x: visible !important; overflow-y: visible !important; position: static !important; right: auto !important; text-align: left !important; top: auto !important; vertical-align: baseline !important; width: auto !important; box-sizing: content-box !important; font-family: Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important; font-weight: normal !important; font-style: normal !important; direction: ltr !important; white-space: pre !important; background-position: initial initial !important; background-repeat: initial initial !important; margin: 0px !important; border: 0px !important initial !important initial !important;">`unix:`

`/home/michal/Programowanie/web_bench/socket`

`failed (11: Resource`</div>
<div class="line number3 index2 alt2" style="padding-top: 0px !important; padding-right: 1em !important; padding-bottom: 0px !important; padding-left: 0em !important; outline-width: 0px !important; outline-style: initial !important; outline-color: initial !important; font-size: 1em !important; border-top-left-radius: 0px 0px !important; border-top-right-radius: 0px 0px !important; border-bottom-right-radius: 0px 0px !important; border-bottom-left-radius: 0px 0px !important; background-image: none !important; background-attachment: initial !important; background-origin: initial !important; background-clip: initial !important; background-color: #0a2b1d !important; bottom: auto !important; float: none !important; height: auto !important; left: auto !important; line-height: 1.1em !important; overflow-x: visible !important; overflow-y: visible !important; position: static !important; right: auto !important; text-align: left !important; top: auto !important; vertical-align: baseline !important; width: auto !important; box-sizing: content-box !important; font-family: Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important; font-weight: normal !important; font-style: normal !important; direction: ltr !important; white-space: pre !important; background-position: initial initial !important; background-repeat: initial initial !important; margin: 0px !important; border: 0px !important initial !important initial !important;">`temporarily unavailable) `

`while`

`connecting to upstream`</div>
</div>
</td>
</tr>
</tbody>
</table>

FastCGI 的多进程由 [spawn-fcgi](http://redmine.lighttpd.net/projects/spawn-fcgi/wiki) 产生。

## 结论

你可以看到 Go 赢得了几乎所有的测试用例。web.go 框架的那个不太理想的结果可能是由于它先尝试用指定的 URL 寻找静态页面，然后才会执行处理方法。让我惊讶的是 tornado Python 框架如此之高的性能，尤其是跟 web.py 框架相比而言。我同样对 CherryPy 服务器比 nginx+flup 快感到惊讶 (我使用 web.py+flup+nginx 跑几乎所有的 Python Web 应用)。