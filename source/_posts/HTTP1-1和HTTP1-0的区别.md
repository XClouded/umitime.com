title: HTTP1.1和HTTP1.0的区别
tags:
  - 拓展
date: 2011-08-17 17:17:58
---

**<span style="color: #008000;">来自于： http://blog.csdn.net/yanghehong/article/details/4222594</span>**

翻了下HTTP1.1的协议标准[RFC2616](http://www.ietf.org/rfc/rfc2616.txt)，下面是看到的一些它跟HTTP1.0的差别。

**<span lang="EN-US"><span style="font-size: small; font-family: Times New Roman;">Persistent Connection(</span></span>****<span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';"><span style="font-size: small;">持久连接</span></span><span lang="EN-US"><span style="font-family: Times New Roman;"><span style="font-size: small;">)

</span></span></span>**

<span style="font-size: small;"><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">在</span><span lang="EN-US"><span style="font-family: Times New Roman;">HTTP1.0</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">中，每对</span><span lang="EN-US"><span style="font-family: Times New Roman;">Request/Response</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">都使用一个新的连接。</span></span><span lang="EN-US">

</span>

<span style="font-size: small;"><span lang="EN-US"><span style="font-family: Times New Roman;">HTTP 1.1</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">则支持</span><span lang="EN-US"><span style="font-family: Times New Roman;">Persistent Connection, </span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">并且默认使用</span></span><span lang="EN-US"><span style="font-size: small; font-family: Times New Roman;">persistent connection.

</span></span>

<span style="font-size: small;">**<span lang="EN-US"><span style="font-family: Times New Roman;">Host </span></span>****<span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">域</span>**</span>

<span style="font-size: small;"><span lang="EN-US"><span style="font-family: Times New Roman;">HTTP1.1</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">在</span><span lang="EN-US"><span style="font-family: Times New Roman;">Request</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">消息头里头多了一个</span><span lang="EN-US"><span style="font-family: Times New Roman;">Host</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">域，比如：</span></span>

<span lang="EN-US"><span style="font-size: small;"><span style="font-family: Times New Roman;">GET /pub/WWW/TheProject.html HTTP/1.1</span></span></span>

<span lang="EN-US"><span style="font-size: small;"><span style="font-family: Times New Roman;">Host: www.w3.org</span></span></span>

<span style="font-size: small;"><span lang="EN-US"><span style="font-family: Times New Roman;">HTTP1.0</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">则没有这个域。</span></span>

<span style="font-size: small;"><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">可能</span><span lang="EN-US"><span style="font-family: Times New Roman;">HTTP1.0</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">的时候认为，建立</span><span lang="EN-US"><span style="font-family: Times New Roman;">TCP</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">连接的时候已经指定了</span><span lang="EN-US"><span style="font-family: Times New Roman;">IP</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">地址，这个</span><span lang="EN-US"><span style="font-family: Times New Roman;">IP</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">地址上只有一个</span><span lang="EN-US"><span style="font-family: Times New Roman;">host</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">。</span></span>

<span id="more-343"></span>

**<span lang="EN-US"><span style="font-size: small; font-family: Times New Roman;">date/time stamp (</span></span>****<span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';"><span style="font-size: small;">日期时间戳</span></span><span lang="EN-US"><span style="font-size: small;"><span style="font-family: Times New Roman;">)

</span></span></span>**

<span style="font-size: small;">_<span lang="EN-US"><span style="font-family: Times New Roman;">(</span></span>__<span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">接收方向</span><span lang="EN-US"><span style="font-family: Times New Roman;">)</span></span>_</span>

<span style="font-size: small;"><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">无论是</span><span lang="EN-US"><span style="font-family: Times New Roman;">HTTP1.0</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">还是</span><span lang="EN-US"><span style="font-family: Times New Roman;">HTTP1.1</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">，都要能解析下面三种</span><span lang="EN-US"><span style="font-family: Times New Roman;">date/time stamp</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">：</span></span>

<span lang="EN-US"><span style="font-size: small;"><span style="font-family: Times New Roman;">Sun, 06 Nov 1994 08:49:37 GMT; RFC 822, updated by RFC 1123</span></span></span>

<span lang="EN-US"><span style="font-size: small;"><span style="font-family: Times New Roman;">Sunday, 06-Nov-94 08:49:37 GMT ; RFC 850, obsoleted by RFC 1036</span></span></span>

<span lang="EN-US"><span style="font-size: small;"><span style="font-family: Times New Roman;">Sun Nov6 08:49:37 1994; ANSI C&#8217;s asctime() format</span></span></span>

<span style="font-size: small;">_<span lang="EN-US"><span style="font-family: Times New Roman;">(</span></span>__<span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">发送方向</span><span lang="EN-US"><span style="font-family: Times New Roman;">)</span></span>_</span><span lang="EN-US">

<span style="font-family: Times New Roman;"><span style="font-size: small;">HTTP1.0</span></span></span><span style="font-size: small;"><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">要求不能生成第三种</span><span lang="EN-US"><span style="font-family: Times New Roman;">asctime</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">格式的</span><span lang="EN-US"><span style="font-family: Times New Roman;">date/time stamp</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">；</span></span>

<span style="font-size: small;"><span lang="EN-US"><span style="font-family: Times New Roman;">HTTP1.1</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">则要求只生成</span><span lang="EN-US"><span style="font-family: Times New Roman;">RFC 1123(</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">第一种</span><span lang="EN-US"><span style="font-family: Times New Roman;">)</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">格式的</span><span lang="EN-US"><span style="font-family: Times New Roman;">date/time stamp</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">。</span></span>

**<span lang="EN-US"><span style="font-size: small;"><span style="font-family: Times New Roman;">Transfer Codings</span></span></span>**

<span style="font-size: small;"><span lang="EN-US"><span style="font-family: Times New Roman;">HTTP1.1</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">支持</span><span lang="EN-US"><span style="font-family: Times New Roman;">chunked transfer</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">，所以可以有</span><span lang="EN-US"><span style="font-family: Times New Roman;">Transfer-Encoding</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">头部域</span><span lang="EN-US"><span style="font-family: Times New Roman;">:</span></span></span>

<span lang="EN-US"><span style="font-size: small; font-family: Times New Roman;">Transfer-Encoding: chunked</span></span>

<span style="font-size: small;"><span lang="EN-US"><span style="font-family: Times New Roman;">HTTP1.0</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">则没有。</span></span>

**<span lang="EN-US"><span style="font-size: small;"><span style="font-family: Times New Roman;">Quality Values</span></span></span>**

<span style="font-size: small;"><span lang="EN-US"><span style="font-family: Times New Roman;">HTTP1.1</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">多了个</span><span lang="EN-US"><span style="font-family: Times New Roman;">qvalue</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">域：</span></span>

<span lang="EN-US"><span style="font-size: small;"><span style="font-family: Times New Roman;">qvalue= ( &#8220;0&#8243; [ "." 0*3DIGIT ] )</span></span></span>

<span lang="EN-US"><span style="font-size: small;"><span style="font-family: Times New Roman;">| ( &#8220;1&#8243; [ "." 0*3("0") ] )</span></span></span>

**<span lang="EN-US"><span style="font-size: small;"><span style="font-family: Times New Roman;">Entity Tags</span></span></span>**

<span style="font-size: small;"><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">用于</span><span lang="EN-US"><span style="font-family: Times New Roman;">Cache</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">。</span></span>

<span style="font-size: small;">**<span lang="EN-US"><span style="font-family: Times New Roman;">Range </span></span>****<span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">和</span><span lang="EN-US"><span style="font-family: Times New Roman;"> Content-Range</span></span>**</span>

<span style="font-size: small;"><span lang="EN-US"><span style="font-family: Times New Roman;">HTTP1.1</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">支持传送内容的一部分。比方说，当客户端已经有内容的一部分，为了节省带宽，可以只向服务器请求一部分。</span></span>

**<span lang="EN-US"><span style="font-size: small;"><span style="font-family: Times New Roman;">100 (Continue) Status</span></span></span>**

<span style="font-size: small;"><span lang="EN-US"><span style="font-family: Times New Roman;">100 (Continue) </span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">状态代码的使用，允许客户端在发</span><span lang="EN-US"><span style="font-family: Times New Roman;">request</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">消息</span><span lang="EN-US"><span style="font-family: Times New Roman;">body</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">之前先用</span><span lang="EN-US"><span style="font-family: Times New Roman;">request header</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">试探一下</span><span lang="EN-US"><span style="font-family: Times New Roman;">server</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">，看</span><span lang="EN-US"><span style="font-family: Times New Roman;">server</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">要不要接收</span><span lang="EN-US"><span style="font-family: Times New Roman;">request body</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">，再决定要不要发</span><span lang="EN-US"><span style="font-family: Times New Roman;">request body</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">。</span></span>

<span style="font-size: small;"><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">客户端在</span><span lang="EN-US"><span style="font-family: Times New Roman;">Request</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">头部中包含</span></span>

<span lang="EN-US"><span style="font-size: small; font-family: Times New Roman;">Expect: 100-continue</span></span>

<span style="font-size: small;"><span lang="EN-US"><span style="font-family: Times New Roman;">Server</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">看到之后呢如果回</span><span lang="EN-US"><span style="font-family: Times New Roman;">100 (Continue) </span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">这个状态代码，客户端就继续发</span><span lang="EN-US"><span style="font-family: Times New Roman;">request body</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">。</span></span>

<span style="font-size: small;"><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">这个是</span><span lang="EN-US"><span style="font-family: Times New Roman;">HTTP1.1</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">才有的。</span></span>

**<span lang="EN-US"><span style="font-size: small;"><span style="font-family: Times New Roman;">Request method</span></span></span>**

<span style="font-size: small;"><span lang="EN-US"><span style="font-family: Times New Roman;">HTTP1.1</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">增加了</span><span lang="EN-US"><span style="font-family: Times New Roman;">OPTIONS, PUT, DELETE, TRACE, CONNECT</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">这些</span><span lang="EN-US"><span style="font-family: Times New Roman;">Request</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">方法</span><span lang="EN-US"><span style="font-family: Times New Roman;">.</span></span></span>

<span lang="EN-US"><span style="font-size: small;"><span style="font-family: Times New Roman;">Method= **&#8220;OPTIONS&#8221;**; Section 9.2</span></span></span>

<span lang="EN-US"><span style="font-size: small;"><span style="font-family: Times New Roman;">| &#8220;GET&#8221;; Section 9.3</span></span></span>

<span lang="EN-US"><span style="font-size: small;"><span style="font-family: Times New Roman;">| &#8220;HEAD&#8221;; Section 9.4</span></span></span>

<span lang="EN-US"><span style="font-size: small;"><span style="font-family: Times New Roman;">| &#8220;POST&#8221;; Section 9.5</span></span></span>

<span lang="EN-US"><span style="font-size: small;"><span style="font-family: Times New Roman;">**| &#8220;PUT&#8221;**; Section 9.6</span></span></span>

<span lang="EN-US"><span style="font-size: small;"><span style="font-family: Times New Roman;">**| &#8220;DELETE&#8221; **; Section 9.7</span></span></span>

<span lang="EN-US"><span style="font-size: small;"><span style="font-family: Times New Roman;">**| &#8220;TRACE&#8221;**; Section 9.8</span></span></span>

<span lang="EN-US"><span style="font-size: small;"><span style="font-family: Times New Roman;">**| &#8220;CONNECT&#8221; **; Section 9.9</span></span></span>

<span lang="EN-US"><span style="font-size: small;"><span style="font-family: Times New Roman;">| extension-method</span></span></span>

<span lang="EN-US"><span style="font-size: small;"><span style="font-family: Times New Roman;">extension-method = token</span></span></span>

**<span lang="EN-US"><span style="font-size: small;"><span style="font-family: Times New Roman;">Status code</span></span></span>**

<span style="font-size: small;"><span lang="EN-US"><span style="font-family: Times New Roman;">HTTP1.1 </span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">增加的新的</span><span lang="EN-US"><span style="font-family: Times New Roman;">status code</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">：</span></span>

_<span style="font-size: 9pt;" lang="EN-US"><span style="font-family: Times New Roman;">(HTTP1.0</span></span>__<span style="font-size: 9pt; font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">没有定义任何具体的</span>__<span style="font-size: 9pt;" lang="EN-US"><span style="font-family: Times New Roman;">1xx status code, HTTP1.1</span></span>__<span style="font-size: 9pt; font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">有</span>__<span style="font-size: 9pt;" lang="EN-US"><span style="font-family: Times New Roman;">2</span></span>__<span style="font-size: 9pt; font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">个</span>__<span style="font-size: 9pt;" lang="EN-US"><span style="font-family: Times New Roman;">)</span></span>_

<span style="font-size: 9pt;" lang="EN-US"><span style="font-family: Times New Roman;">100 Continue</span></span>

<span style="font-size: 9pt;" lang="EN-US"><span style="font-family: Times New Roman;">101 Switching Protocols</span></span>

<span style="font-size: 9pt;" lang="EN-US"><span style="font-family: Times New Roman;">203 Non-Authoritative Information</span></span>

<span style="font-size: 9pt;" lang="EN-US"><span style="font-family: Times New Roman;">205 Reset Content</span></span>

<span style="font-size: 9pt;" lang="EN-US"><span style="font-family: Times New Roman;">206 Partial Content</span></span>

<span style="font-size: 9pt;" lang="EN-US"><span style="font-family: Times New Roman;">302 Found (</span></span>_<span style="font-size: 9pt; font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">在</span>__<span style="font-size: 9pt;" lang="EN-US"><span style="font-family: Times New Roman;">HTTP1.0</span></span>__<span style="font-size: 9pt; font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">中有个</span>_<span style="font-family: Times New Roman;">_<span style="font-size: 9pt;"> <span lang="EN-US">302 Moved Temporarily</span></span>_<span style="font-size: 9pt;" lang="EN-US">)</span></span>

<span style="font-size: 9pt;" lang="EN-US"><span style="font-family: Times New Roman;">303 See Other</span></span>

<span style="font-size: 9pt;" lang="EN-US"><span style="font-family: Times New Roman;">305 Use Proxy</span></span>

<span style="font-size: 9pt;" lang="EN-US"><span style="font-family: Times New Roman;">307 Temporary Redirect</span></span>

<span style="font-size: 9pt;" lang="EN-US"><span style="font-family: Times New Roman;">405 Method Not Allowed</span></span>

<span style="font-size: 9pt;" lang="EN-US"><span style="font-family: Times New Roman;">406 Not Acceptable</span></span>

<span style="font-size: 9pt;" lang="EN-US"><span style="font-family: Times New Roman;">407 Proxy Authentication Required</span></span>

<span style="font-size: 9pt;" lang="EN-US"><span style="font-family: Times New Roman;">408 Request Timeout</span></span>

<span style="font-size: 9pt;" lang="EN-US"><span style="font-family: Times New Roman;">409 Conflict</span></span>

<span style="font-size: 9pt;" lang="EN-US"><span style="font-family: Times New Roman;">410 Gone</span></span>

<span style="font-size: 9pt;" lang="EN-US"><span style="font-family: Times New Roman;">411 Length Required</span></span>

<span style="font-size: 9pt;" lang="EN-US"><span style="font-family: Times New Roman;">412 Precondition Failed</span></span>

<span style="font-size: 9pt;" lang="EN-US"><span style="font-family: Times New Roman;">413 Request Entity Too Large</span></span>

<span style="font-size: 9pt;" lang="EN-US"><span style="font-family: Times New Roman;">414 Request-URI Too Long</span></span>

<span style="font-size: 9pt;" lang="EN-US"><span style="font-family: Times New Roman;">415 Unsupported Media Type</span></span>

<span style="font-size: 9pt;" lang="EN-US"><span style="font-family: Times New Roman;">416 Requested Range Not Satisfiable</span></span>

<span style="font-size: 9pt;" lang="EN-US"><span style="font-family: Times New Roman;">417 Expectation Failed</span></span>

<span style="font-size: 9pt;" lang="EN-US"><span style="font-family: Times New Roman;">504 Gateway Timeout</span></span>

<span style="font-size: 9pt;" lang="EN-US"><span style="font-family: Times New Roman;">505 HTTP Version Not Supported</span></span>

**<span lang="EN-US"><span style="font-size: small;"><span style="font-family: Times New Roman;">Content Negotiation</span></span></span>**

<span style="font-size: small;"><span lang="EN-US"><span style="font-family: Times New Roman;">HTTP1.1</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">增加了</span><span lang="EN-US"><span style="font-family: Times New Roman;">Content Negotiation</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">，分为</span><span lang="EN-US"><span style="font-family: Times New Roman;">Server-driven Negotiation</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">，</span><span lang="EN-US"><span style="font-family: Times New Roman;">Agent-driven Negotiation</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">和</span><span lang="EN-US"><span style="font-family: Times New Roman;">Transparent Negotiation</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">三种。</span></span>

<span style="font-size: small;">**<span lang="EN-US"><span style="font-family: Times New Roman;">Cache (</span></span>****<span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">缓存</span><span lang="EN-US"><span style="font-family: Times New Roman;">)</span></span>**</span>

<span style="font-size: small;"><span lang="EN-US"><span style="font-family: Times New Roman;">HTTP1.1(RFC2616)</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">详细展开地描述了</span><span lang="EN-US"><span style="font-family: Times New Roman;">Cache</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">机制，详见</span><span lang="EN-US"><span style="font-family: Times New Roman;">13</span></span><span style="font-family: 宋体; mso-ascii-font-family: 'Times New Roman'; mso-hansi-font-family: 'Times New Roman';">节。</span></span>

依据：

[rfc2616 Hypertext Transfer Protocol &#8212; HTTP-1.1.txt](http://www.ietf.org/rfc/rfc2616.txt)

[rfc1945 Hypertext Transfer Protocol &#8212; HTTP 1.0.txt](http://www.ietf.org/rfc/rfc1945.txt)