title: CORS跨域试用
tags:
  - JavaScript
  - CORS
  - 跨域
date: 2012-08-20 14:11:02
---

CORS，全称Cross-Origin Resource Sharing，中文翻译为 跨源资源共享，目前支持情况： IE8+, FF3.5+, Safari 4+, Opera 12+(? opera不是很明确，但是至少我的opera V12是测试通过的)。

测试使用nginx配置两个虚拟主机（修改hosts，弄两个本地域名），node.js为后端服务器（nginx代理一个虚拟主机转发给nodejs）。

http://a.com/cors-test.html内容如下：

<pre>&lt;!DOCTYPE html&gt;
&lt;html lang="zh-CN"&gt;
&lt;head&gt;
    &lt;meta charset="UTF-8"&gt;
    &lt;title&gt;测试CORS跨域&lt;/title&gt;
&lt;/head&gt;
&lt;body&gt;
    &lt;h3&gt;跨域显示&lt;/h3&gt;
    &lt;p id="text"&gt;&lt;/p&gt;

    &lt;script type="text/javascript"&gt;
        function sendCORSRequest(method, url) {
            var xhr = new XMLHttpRequest();
            if('withCredentials' in xhr) {
                xhr.open(method, url, true);
            } else if(typeof XDomainRequest != 'undefined') {
                xhr = new XDomainRequest();
                xhr.open(method, url);
            } else {
                xhr = null;
            };
            return xhr;
        };
        window.onload = function() {
            var request = sendCORSRequest('POST', 'http://cors.b.com/username/');
            var $show = document.getElementById('text');
            if(request) {
                request.onload = function(data) {
                    data = request.responseText;
                    $show.innerHTML = data;
                };
                request.onerror = function() {
                    console.info('cors is error.....')
                };
                request.send();
            } else {
                $show.innerHTML = '不支持CORS跨域....';
            };
        };
    &lt;/script&gt;
&lt;/body&gt;
&lt;/html&gt;</pre>

nginx转发http://cors.b.com/username/请求给服务器端nodejs的写法也很简单：

<pre>var http = require('http');

http.createServer(function(request, response) {
    response.writeHead(200, {
        'Content-Type': 'text/plain',
        'Access-Control-Allow-Origin': 'http://cors.a.com'
    });

    response.write('myname is tofishes');

    response.end();
}).listen(8080);</pre>

以上测试通过。

补充（http://www.weste.net/2011/4-27/75028.html）：

Firefox, Safari, 和Chrome的XMLHttpRequest对象与IE的XDomainRequest对象有着相似的充分的接口，这些模式运行的很好。常见的接口属性/方法:

*   abort()——用来终止已在进程中请求。
*   Onerror()——替代onreadystatechange方法来探测错误。
*   Onload()——替代onreadystatechange方法来探测成功。
*   responseText——用来取得响应地文本。
*   send()——用来发送请求。

### Preflighted请求

除了GET或POST，通过一种称之为**preflighted**请求的服务器透明验证机制，CORS允许使用自定义的头部和方法，以及不同主体内容类型。当你尝试使用高级选项中的一个来试着建立一个请求时，这时就建立了一个preflighted请求。该请求使用可选的方法，并发送如下头部：

*   Origin——与简单请求相同。
*   Access-Control-Request-Method——请求将要使用的方法。
*   Access-Control-Request-Headers——（可选）一个逗号分开的正被使用的自定义头部列表。

例子假定一个头部自定义为NCZ的POST请求：

> Origin: http://www.nczonline.net
> 
> Access-Control-Request-Method: POST
> 
> Access-Control-Request-Headers: NCZ

在请求期间，服务器能决定是否允许这类请求。服务器通过在响应中发送以下头部来与浏览器通信。

*   Access-Control-Allow-Origin——与简单请求相同。
*   Access-Control-Allow-Methods——用逗号分开的可接受的方法列表。
*   Access-Control-Allow-Headers——用逗号分开的服务器可接受的头部列表。
*   Access-Control-Max-Age——preflighted 请求应该被缓存的时间。

如：

> Access-Control-Allow-Origin: http://www.nczonline.net
> 
> Access-Control-Allow-Methods: POST, GET
> 
> Access-Control-Allow-Headers: NCZ
> 
> Access-Control-Max-Age: 1728000

preflighted 请求一旦作出，结果将按响应中规定的时间缓存下来；第一次做出这样的请求，你将引发一次额外的HTTP请求。

Firefox 3.5+, Safari 4+和Chrome都支持preflighted 请求，IE8则不支持。

### Credentialed请求

默认状态下，跨域请求不提供证书（cookie、HTTP身份验证、客户端SSL证书）。你可以规定一个请求应该通过设置withCredentials属性为true来发送证书。如果服务器允许credentialed请求，那么它将用下面的头部作出响应：

如果一个**credentialed**请求被发送，这个头部不会作为响应地一部分被发送。浏览器不会将响应传递给JavaScript(responseText是一个空字符串，状态为 0，onerror()被调用)。注意，服务器也能发送这个HTTP头部作为preflight响应的一部分，以此来表明该源允许发送 credentialed请求。

> Access-Control-Allow-Credentials: true

IE8不支持withCredentials属性，Firefox 3.5+, Safari 4+和Chrome都支持它。

&nbsp;