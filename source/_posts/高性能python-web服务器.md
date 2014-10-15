title: 高性能python web服务器
tags:
  - python
date: 2011-08-17 16:40:19
---

<span style="color: #008000;">**来自于： http://www.webguo.com/2011/01/24/python_high_performance_webserver.html**</span>

<span style="color: #008000;">**还有一篇外篇测试：http://nichol.as/benchmark-of-python-web-servers **</span>

最近在物色高性能的python web服务器。一般来说，web服务器常见的有nginx，apache，lighttpd等，我是个nginx控，所以一开始就把apache，lighttpd给抛弃了，采用nginx作为前端。

当然，前端固然很重要，但更加重要的是后端的处理服务器，以不同模式为基础构建的web app服务器决定着整个系统的响应快慢。

通过参考Nicholas Piel写的[《Benchmark of Python WSGI Servers》](http://nichol.as/benchmark-of-python-web-servers)我圈定了以下几个服务器（模块）：[mod_wsgi for nginx](http://wiki.nginx.org/NginxNgxWSGIModule)，[fapws3](http://www.fapws.org/)， [gevent](http://www.gevent.org/)，[uwsgi](http://projects.unbit.it/uwsgi/)，以及陪同测试的django自带FastCGI prefork/threaded模式。

mod_wsgi代表nginx原配python wsgi协议支持，fapws3是Fast Asynchronous Python Web Server的简称，使用processor/thread模型，gevent是走的lightweight threads路线，uwsgi采用的是nginx的模块+自身socket多processor/thread模型，之所以没选择callback/generator类型的服务器是因为都还不成熟，在Nicholas的那篇文章里面可以看出他们丢包率以及性能都不尽人意。

首先，介绍硬件情况：公司一台年久失修的服务器，P4 2G单核，512M内存，1.5G Swap，Ubuntu 8.10 Desktop，上面还开了个Nagios监控器和个FireFox，性能绝对是你能想象出的底线。原本服务器上跑了个Apache2，因为很久没有使用apache2了，实在是懒得去翻document找如何配置，就又装了个nginx 0.8.35，今天升级到了0.8.36，性能上2者基本毫无区别，36版只是针对35的bug进行了修复，所以对结果没有实质上的影响。django 1.1+flup作陪同的FastCGI prefork/threaded模式测试，测试软件采用Apache Benchmark Version 2.3。

<span id="more-329"></span>

首先是Django的配置，生成一个新的项目，直接访问其默认页面，项目启动参数分别为

1.  <span><span>python manage.py runfcgi method=threaded host=127.0.0.1 port=3033 daemonize=</span><span class="keyword">false</span></span>
2.  <span>python manage.py runfcgi method=prefork host=127.0.0.1 port=3033 daemonize=<span class="keyword">false</span></span>
3.  <span>python manage.py runfcgi method=threaded host=127.0.0.1 port=3033 daemonize=<span class="keyword">false</span><span> minspare=50 maxspare=50 </span></span>

为什么第三个不同，是因为参考了JavaEye上的一篇评测，采用静态进程池的话性能会比动态线程生成高2倍左右（[评测原文](http://www.javaeye.com/topic/267429)）

Nginx的FastCGI服务器参数采用默认参数

在ab -n 1000 -c 20的情况下，5次运行得到的Request Per Second分别如下：

<table border="0">
<tbody>
<tr>
<td>nginx+fcgi+prefork</td>
<td>44.06</td>
<td>54.93</td>
<td>42.6</td>
<td>41.08</td>
<td>41.89</td>
</tr>
<tr>
<td>nginx+fcgi+thread</td>
<td>242.12</td>
<td>195.96</td>
<td>243.69</td>
<td>209.59</td>
<td>204.59</td>
</tr>
<tr>
<td>nginx+fcgi+prefork+spare</td>
<td>119.51</td>
<td>202.89</td>
<td>177.6</td>
<td>206.72</td>
<td>206.85</td>
</tr>
</tbody>
</table>

可见使用静态进程池之后进程模式的性能有大幅提高。本来下一个是Nginx+mod_wsgi的测试，很可惜的是，不知道是配置原因还是其他什么问题，在4个连接测试完之后mod_wsgi就死掉了，因此这次测试中只好放弃。

接着是fapws3，号称最快的Python WSGI服务器，我们来看看起到底有多快。

PS: 安装的时候需要安装libev-dev包，fapws3是基于libev技术实现的。

使用的Django Wrapper如下：

1.  <span><span class="keyword">import</span><span> fapws._evwsgi as evwsgi </span></span>
2.  <span><span class="keyword">from</span><span> fapws </span><span class="keyword">import</span><span> base </span></span>
3.  <span><span class="keyword">import</span><span> time </span></span>
4.  <span><span class="keyword">import</span><span> sys, os </span></span>
5.  <span>sys.setcheckinterval(<span class="number">100000</span><span>) </span></span>
6.  <span>sys.path.append(<span class="string">&#8216;/opt/www/djtest/&#8217;</span><span>) </span></span>
7.  <span>os.environ[<span class="string">'DJANGO_SETTINGS_MODULE'</span><span>] = </span><span class="string">&#8216;settings&#8217;</span></span>
8.  <span><span class="keyword">from</span><span> fapws.contrib </span><span class="keyword">import</span><span> django_handler, views </span></span>
9.  <span><span class="keyword">import</span><span> django </span></span>
10.11.  <span><span class="keyword">def</span><span> start(): </span></span>
12.  <span> evwsgi.start(<span class="string">&#8220;0.0.0.0&#8243;</span><span>, </span><span class="number">98</span><span>) </span></span>
13.14.  <span> evwsgi.set_base_module(base) </span>
15.16.  <span> <span class="keyword">def</span><span> generic(environ, start_response): </span></span>
17.  <span> res=django_handler.handler(environ, start_response) </span>
18.  <span> <span class="keyword">return</span><span> [res] </span></span>
19.20.  <span> mediafile=views.Staticfile(django.__path__[<span class="number">0</span><span>] + </span><span class="string">&#8216;/contrib/admin/media/&#8217;</span><span>, maxage=</span><span class="number">2629000</span><span>) </span></span>
21.  <span> evwsgi.wsgi_cb((<span class="string">&#8220;/media/&#8221;</span><span>,mediafile)) </span></span>
22.  <span> evwsgi.wsgi_cb((<span class="string">&#8221;</span><span>,generic)) </span></span>
23.24.  <span> evwsgi.set_debug(<span class="number">0</span><span>) </span></span>
25.  <span> evwsgi.run() </span>
26.27.  <span><span class="keyword">if</span><span> __name__==</span><span class="string">&#8220;__main__&#8221;</span><span>: </span></span>
28.  <span> start() </span>

结果如下：

<table border="0">
<tbody>
<tr>
<td>fapws</td>
<td>379.32</td>
<td>406.58</td>
<td>413.83</td>
<td>399.29</td>
<td>414.61</td>
</tr>
<tr>
<td>nginx+proxy+fapws</td>
<td>328.61</td>
<td>337.2</td>
<td>326.6</td>
<td>262.6</td>
<td>274.57</td>
</tr>
</tbody>
</table>

不错，非常不错，将近400的RPS，即便是被NGINX包了一层反向连接的情况下依然还有平均300上下的RPS表现，很对得起自己的称号，唯独美中不足的是Fapws不支持HTTP1.1，其官网也是说HTTP1.1是RoadMap中计划的，什么时候实现？别问我~

然后是uWSGI，这个WEB服务器有点特殊，它本身支持作为其他服务器的模块而存在如nginx，apache，cherokee等，但是其自身也是需要同时打开作为一个socket存在于系统之中，你可以开多个socket文件，这样就可以通过nginx实现单机分流，只不过谁知道有没有用捏。

安装方法参考nginx+mod_wsgi的方法安装，不同的是加入nginx模块后还需要编译一个对应本机python版本的uwsgi放入/usr/bin目录下然后用如下命令启动。

启动命令：

1.  <span><span>uwsgi -s /tmp/uwsgi.sock -C -x /usr/local/www/djtest/uwsgi.xml -d /var/log/uwsgi.log &amp; </span></span>

uwsgi.xml

<div class="dp-highlighter">
<div class="bar">
<div class="tools">[源码](http://www.oschina.net/bbs/thread/10751#)[打印](http://www.oschina.net/bbs/thread/10751#)[？](http://www.oschina.net/bbs/thread/10751#)</div>
</div>

1.  <span><span>/opt/www </span></span>
2.3.4.

uwsgi.py

1.  <span><span class="keyword">import</span><span> os, sys </span></span>
2.  <span>sys.path.append(<span class="string">&#8216;/opt/www/djtest/&#8217;</span><span>) </span></span>
3.  <span>os.environ[<span class="string">'DJANGO_SETTINGS_MODULE'</span><span>] = </span><span class="string">&#8216;settings&#8217;</span></span>
4.  <span><span class="keyword">import</span><span> django.core.handlers.wsgi </span></span>
5.  <span>application = django.core.handlers.wsgi.WSGIHandler() </span>

结果不是很好，但也不是很挫。

<table border="0">
<tbody>
<tr>
<td>nginx+uwsgi</td>
<td>294.78</td>
<td>248.21</td>
<td>304.98</td>
<td>296.5</td>
<td>304.43</td>
</tr>
</tbody>
</table>

最后是Gevent，这个时候我将nginx换成了0.8.36，好在对于结果实际上是没什么影响的，性能误差在2%以内。

安装Gevent费了不少精神，其官网貌似被墙，中英文资料特别少，首先得安装libevent1.4以上，ubuntu自带的1.3是不行的，编译会报错，还得用easy_install greenlet。安装完这2个后编译使用-I -L参数定位到你安装的libevent目录下lib目录和include目录，还需要将lib目录下的所有文件做个软连接到/usr/lib目录。编译好后，就可以开始使用了。

Django Wrapper代码如下：

1.  <span><span class="keyword">from</span><span> gevent </span><span class="keyword">import</span><span> wsgi </span></span>
2.  <span><span class="keyword">from</span><span> djtest.wsgi </span><span class="keyword">import</span><span> application </span></span>
3.  <span>wsgi.WSGIServer((<span class="string">&#8221;</span><span>, </span><span class="number">95</span><span>), application, spawn=</span><span class="special">None</span><span>).serve_forever() </span></span>
</div>

结果如下：

<table border="0">
<tbody>
<tr>
<td>gevent</td>
<td>330.18</td>
<td>330.36</td>
<td>329.76</td>
<td>320.66</td>
<td>277.37</td>
</tr>
<tr>
<td>nginx+proxy+gevent</td>
<td>287.86</td>
<td>286.69</td>
<td>288.22</td>
<td>280.37</td>
<td>288.28</td>
</tr>
</tbody>
</table>

令人惊讶的性能，但还是不及fapws。

最后是变态版的高压力测试，ab -n 10000 -c 500，估计是这台服务器的极限了。django的FastCGI模式没有加入测试，原因是太慢了，不想等，只测试了fapws，nginx+proxy+fapws，gevent，nginx+proxy+gevent，nginx+uwsgi，结果如下：

<table border="0">
<tbody>
<tr>
<td>gevent</td>
<td>192.58</td>
</tr>
<tr>
<td>nginx+proxy+gevent</td>
<td>164.84</td>
</tr>
<tr>
<td>fapws</td>
<td>288.14</td>
</tr>
<tr>
<td>nginx+proxy+fapws</td>
<td>221.01</td>
</tr>
<tr>
<td>nginx+uwsgi</td>
<td>173.601</td>
</tr>
</tbody>
</table>

fapws再次胜出，不过可以看出性能也下降了不少。值得说明的是这次测试中各种服务器均出了一点状况，单用fapws会在ab测试完之后提示大量连接被关闭，无法Response，但ab那边显示0个failed connection，fapws+nginx则不会出现，uwsgi比较搞，若出现failed connection，ab报告中server name就是nginx，完整的通过测试就是空白，难道错误都是nginx一个人的事么？gevent的一个奇特现象是在nginx包了一层的情况下稳定性竟然还不如单用gevent，这点很是奇怪，nginx只做了个反向连接哎？这完全是不应该发生的呐。

总结：如果你是个冒险者，fapws绝对是你最佳的选择，性能没话说，高并发也不是那么糟（我觉得ab不是那么靠谱），美中不足的是其不支持HTTP1.1协议。如果你担心其稳定性，用nginx包一层，只要fapws坏了你就立刻能从访问页面上得到信息，当然包一层是以性能损失为代价的，这就看自己的选择了。如果你期望用一个nginx统一后端各种CGI语言的运行，uWSGI挺不错的，类似于php-fpm的启动模式，还可以做单机分流，配合nginx强大的高并发负载能力，无论在什么条件下均有优异的表现，蛋疼的是稳定性还有所欠佳，高并发时有的连接会死掉。GEVENT则是新技术模型（lightweight threads）下一个强大的实现，低压的时候接近fapws的性能，高压的时候单用依然可以达到和uwsgi类似的RPS，无奈的是gevent和nginx搭配起来用就有点杯具了，希望以后的版本不会出现这个问题。

<span style="margin: 0px; color: #ff0000; padding: 0px;">2010/4/24 Update:很荣幸Gevent的作者看到了这篇文章提出了一点修改的建议，于是乎今天我又按照他所说的方法重新测试了一下，得到了新的结果。</span>

ab -n 1000 -c 20

<table border="0">
<tbody>
<tr>
<td>gevent</td>
<td>335.21</td>
<td>325.91</td>
<td>315.43</td>
<td>336.77</td>
<td>334.94</td>
</tr>
<tr>
<td>nginx+proxy+gevent</td>
<td>271.02</td>
<td>278.95</td>
<td>263.30</td>
<td>278.58</td>
<td>272.16</td>
</tr>
</tbody>
</table>

ab -n 10000 -c 500

<table border="0">
<tbody>
<tr>
<td>gevent</td>
<td>209.71</td>
<td>242.30</td>
<td>291.20</td>
<td>244.55</td>
<td>265.79</td>
</tr>
<tr>
<td>nginx+proxy+gevent</td>
<td>158.14</td>
<td>158.25</td>
<td>158.39</td>
<td>174.02</td>
<td>158.18</td>
</tr>
</tbody>
</table>

结果太令人惊讶了，Gevent的性能和Fapws相差5%左右，但提供了完整的HTTP1.1支持，更好的高并发访问的稳定性，当然一旦你用上nginx做前端反向代理，性能还是会再下降30%左右。如果是生产环境的话在fapws没有完整的支持HTTP1.1之前Gevent绝对是最好的选择！