title: Nginx配置带www域名转向无www域名
tags:
  - solution
  - 服务器
  - nginx
  - 域名转向
  - 带www
  - 无www
date: 2011-12-13 16:23:39
---

参考：

http://chinaapp.sinaapp.com/?p=774

http://b.lesseverything.com/2008/4/9/redirect-from-www-to-non-www-using-nginx

&nbsp;

比如配置访问www.cssor.com转向cssor.com， 如下：

<pre class="brush:shell">server {
    listen 80;
    server_name www.cssor.com cssor.com;
    if ($host = 'www.cssor.com' ) {
        rewrite ^/(.*)$ http://cssor.com/$1 permanent;
    }
}

#或者,所有的非顶级域名都转过来

    if ($host != 'cssor.com' ) {
        rewrite ^/(.*)$ http://cssor.com/$1 permanent;
    }</pre>

请注意一点$host = &#8216;www.cssor.com&#8217;，这里是一个等号不是两个等号，否则重启nginx会报错：

Restarting nginx: nginx: [emerg] unexpected &#8220;==&#8221; in condition in 。。。

网上资料坑爹呢。。。

另外if和()之间应该有空格，紧贴会报了一个错出来：

Restarting nginx: nginx: [emerg] unknown directive &#8220;if($host&#8221; in 。。。。