title: nginx and tomcat7 in ubuntu 10.04
tags:
  - linux
  - solution
  - 服务器
  - lucid
  - nginx
  - nginx tomcat
  - tomcat7
  - ubuntu
date: 2011-11-01 14:16:43
---

系统为ubuntu 10.04，选用nginx+tomcat7搭建jsp运行环境，nginx处理静态文件，其余动态请求则转发给tomcat7。

首先安装 nginx，我先用的如下命令（参考http://wiki.ubuntu.org.cn/Nginx）：

<pre class="brush:shell">sudo apt-get install nginx</pre>

结果安装的nginx版本相当低：

<pre class="brush:shell">:~$ nginx -V
nginx: nginx version: nginx/0.7.64</pre>

后来在官方看到了官方提供的软件源，虽然很纳闷为什么只提供了ubuntu10.04的源，而没有其他版本的源，倒是也刚好符合当前系统版本。

官方下载地址：http://nginx.org/en/download.html

使用官方源安装更新如下：

<pre class="brush:shell">sudo vi /etc/apt/sources.list
#加入如下内容
deb http://nginx.org/packages/ubuntu/ lucid nginx
deb-src http://nginx.org/packages/ubuntu/ lucid nginx

#然后执行下面的命令即可
sudo apt-get update
sudo apt-get install nginx  (或者命令： aptitude install nginx)</pre>

这样nginx就会安装或更新到最新的稳定版本，当前我的是 nginx 1.0.8。

> Ubuntu安装之后的文件结构大致为：
> 
> 所有的配置文件都在/etc/nginx下，并且每个虚拟主机已经安排在了/etc/nginx/sites-available下
> 
> 程序文件在/usr/sbin/nginx
> 
> 日志放在了/var/log/nginx中
> 
> 并已经在/etc/init.d/下创建了启动脚本nginx
> 
> 默认的虚拟主机的目录设置在了/var/www/nginx-default (有的版本 默认的虚拟主机的目录设置在了/var/www, 请参考/etc/nginx/sites-available里的配置)
> 
> 以上皆参考http://wiki.ubuntu.org.cn/Nginx

管理nginx启动，停止使用如下命令：

<pre class="brush:shell">sudo /etc/init.d/nginx start
sudo /etc/init.d/nginx stop
sudo /etc/init.d/nginx restart</pre>

启动后，访问ip地址，端口默认是80，就可以看到 Welcome to nginx!，这样就表明成功了。

安装tomcat7请参考这里：[ http://www.cssor.com/virtualbox-install-ubuntu-server-10-04-jdk6-apache2-tomcat7.html](http://www.cssor.com/virtualbox-install-ubuntu-server-10-04-jdk6-apache2-tomcat7.html "ubuntu 10.04安装tomcat7")

接下来是配置nginx，确保动态请求转发给tomcat。

<span style="color: #ff0000;">值得一提的是，用官方源安装的nginx已经没有sites-available,sites-enable这两个文件夹了。</span>

<span style="color: #ff0000;">所以虚拟主机 应该配置到/etc/nginx/conf.d 目录下。</span>

<span style="color: #ff0000;">直接 cp default.conf  new-host.conf，然后配置new-host.conf即可。</span>

在new-host.conf中主要配置如下：

<pre class="brush:shell">server {
    listen       80;
    server_name  new-host.example;
    access_log  /var/log/nginx/new-host.example.access.log  main;

    #当其他location无匹配时，默认都转发到tomcat去处理
    #网上有些配置是根据后缀.jsp匹配的，实际应用中很傻
    #尤其是REST的uri风格，后缀无关紧要，动态请求完全可以映射为无后缀uri
    location / {
        proxy_connect_timeout 3;
        proxy_send_timeout 30;
        proxy_read_timeout 30;
        proxy_set_header X-Forwarded-Host $host;
        proxy_set_header X-Forwarded-Server $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass http://new-host.example:8080;
    }
    #将静态文件交给nginx处理而不必转发给tomcat
    #按后缀正则匹配优先级比 / 高，所以放后面也没关系
    location ~ \.(gif|jpg|jpeg|png|css|js)$ {
        root   /home/WebRoot;
        expires 100d;
    }

    #...................其他配置项目略
}</pre>

nginx转发规则配置好，然后用new-host.example的域名配置好tomcat的虚拟主机(编辑server.xml)：

<pre class="brush:shell">&lt;Host name="new-host.example" appBase="/home" uppackWARs="true" autoDeploy="false"&gt;
    &lt;Context docBase="WebRoot" path="" reloadable="true" /&gt;
&lt;/Host&gt;</pre>

最后重启nginx，与tomcat：

<pre class="brush:shell">sudo /etc/init.d/tomcat7 restart
sudo /etc/init.d/nginx restart</pre>