title: javascript跨域之同源策略
tags:
  - JavaScript
  - 同源策略
  - 跨域
date: 2012-08-17 14:52:57
---

来源于：http://www.woiweb.net/same-origin-policy.html

所谓同源是指域名，协议，端口均相同。

同源的几种情况：

1.不同域名属于跨域，如：www.a.com和www.b.com，另外www.a.com 和www.a.com.cn 也属于不同域名。

2.主域名和子域名（二级域名、三级域名等）跨域，如：www.a.com 和 sub.a.com

属于跨域，sub.a.com 和 sub1.a.com 之间也是跨域。

3.不同协议属于跨域，如：http://www.a.com 和 https://www.a.com。

4.不同端口，如： www.a.com:80和 www.a.com:81 。

5.IP和域名属于跨域，如：123.125.106.16 和 www.weibo.com。