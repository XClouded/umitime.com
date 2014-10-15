title: apache反向代理配置一例
tags:
  - 服务器
date: 2011-11-08 12:29:46
---

配置apache虚拟主机一例，使用了反向代理：

<pre class="brush:shell">&lt;VirtualHost *:80&gt;
        ServerAdmin cc@cssor.com
        ServerName cssor.com
        ServerAlias  www.cssor.com
        DocumentRoot /home/www/cssor.com/WebRoot
        &lt;Directory /home/www/cssor.com/WebRoot&gt;
                Options Indexes FollowSymLinks MultiViews
                AllowOverride All
                Order allow,deny
                Allow from all
        &lt;/Directory&gt;

        ProxyPreserveHost On
        ProxyRequests Off
        #排除不需要后端应用处理的目录，由apache接管
        #一般用来排除掉静态文件
        #这里访问 http://cssor.com/res/img/aa.png直接由apache处理
        ProxyPass /res/ !

        ProxyPass / http://localhost:8080/
        #ProxyPassReverse用于隐藏后端域名
        #这里localhost就不会显示到浏览器地址栏，保持域名cssor.com
        ProxyPassReverse / http://localhost:8080/

        ErrorLog /var/log/apache2/cssor.com.log
        CustomLog /var/log/apache2/cssor.com.custom.log common
&lt;/VirtualHost&gt;</pre>