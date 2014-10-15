title: 使用cookie-free domains存放静态资源
tags:
  - 前端优化
  - cookie free
date: 2014-07-23 14:44:16
---

> <span style="color: #111111;">所谓的 cookie-free domains 就是在浏览器发送静态内容的请求时不会发送cookies 的域名。</span>

查了查资料，要做到请求静态资源无Cookie，最简单的做法就是: 使用和当前网站域名完全不同的另一个域名来存放js, images, css等资源。比如当前网站是cssor.com，那么静态资源都放在 umitime.com上，在cssor.com请求umitime.com上的资源时，完全不会有Cookie传递。

还有通过二级域名来做Cookie-free domain的，不过稍有些条件限制。域名那么便宜，随便注册一个做Cookie-free domain就行了。

资料来源：

http://farlee.info/archives/yslow-used-cookie-free-domains-wordpress-blog-website-speed.html

http://undefinedblog.com/cookie-free-domain-configuration-nginx/

http://blog.s-shadow.com/?p=285

http://www.aips.me/wordpress-image-independent-domain-name.html