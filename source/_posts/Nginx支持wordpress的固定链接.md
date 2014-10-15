title: Nginx支持wordpress的固定链接
tags:
  - WordPress
  - 服务器
date: 2012-02-07 11:57:29
---

第一种方式来自 http://blog.chenlb.com/2010/10/nginx-deploy-wordpress.html

<pre class="brush:shell">index  index.php index.html index.htm;
location  / {
        try_files $uri $uri/ /index.php?q=$uri&amp;$args;
}</pre>

第二中方式摘自LNMP一键安装包的配置文件：

<pre class="brush:shell">location / {
if (-f $request_filename/index.html){
                rewrite (.*) $1/index.html break;
        }
if (-f $request_filename/index.php){
                rewrite (.*) $1/index.php;
        }
if (!-f $request_filename){
                rewrite (.*) /index.php;
        }
}</pre>