title: 为Mac配置服务器nginx
tags:
  - 服务器
  - nginx
date: 2014-01-03 00:28:09
---

我们的项目基于SSI技术实现前后端完全分离，同事都用Apache，我比较喜欢nginx。

安装方法源自：http://www.codingcool.com/2013/07/18/在mac-os-x-10-9上编译安装nginx/

接着往下转：<span id="more-1053"></span>

### 1.先安装PCRE库(转注：nginx rewrite依赖该库)

可以在这里[下载](http://www.pcre.org/)最新版，我这里使用的是8.33的版本然后在终端执行下面的命令。

<pre>cd ~/Download
tar xvzf pcre-8.33.tar.gz
cd pcre-8.12
sudo ./configure --prefix=/usr/local
sudo make
sudo make install</pre>

### 2.下载安装nginx

首先在[nginx官网](http://nginx.org/ "nginx")下载最新的源码，我这里用的是[nginx-1.5.2](http://nginx.org/download/nginx-1.5.2.tar.gz)

<pre>tar -zvxf nginx-1.5.2.tar.gz
cd nginx-1.5.2
./configure
make
make install</pre>

默认编译概要：

<pre>Configuration summary
+ using system PCRE library
+ OpenSSL library is not used
+ md5: using system crypto library
+ sha1: using system crypto library
+ using system zlib library

# 默认编译参数对应的安装路径（*_temp 为目录）
nginx path prefix: "/usr/local/nginx"
nginx binary file: "/usr/local/nginx/sbin/nginx"
nginx configuration prefix: "/usr/local/nginx/conf"
nginx configuration file: "/usr/local/nginx/conf/nginx.conf"
nginx pid file: "/usr/local/nginx/logs/nginx.pid"
nginx error log file: "/usr/local/nginx/logs/error.log"
nginx http access log file: "/usr/local/nginx/logs/access.log"
nginx http client request body temporary files: "client_body_temp"
nginx http proxy temporary files: "proxy_temp"
nginx http fastcgi temporary files: "fastcgi_temp"
nginx http uwsgi temporary files: "uwsgi_temp"
nginx http scgi temporary files: "scgi_temp"</pre>

为了方便：(转注：我选择这个方式)

<pre>sudo ln -s /usr/local/nginx/sbin/nginx /usr/local/bin/nginx
sudo ln -s /usr/local/nginx/conf /etc/nginx
sudo ln -s /usr/local/nginx/logs/nginx.pid /var/run/nginx.pid
sudo ln -s /usr/local/nginx/logs /var/log/nginx</pre>

或者直接在编译时设定

<pre>./configure \
--prefix=/usr/local \
--sbin-path=/usr/local/sbin \
--conf-path=/etc/nginx \
--pid-path=/var/run \
--error-log-path=/var/log/nginx \
--http-log-path=/var/log/nginx</pre>

编译参数参考 [Nginx InstallOption](http://wiki.nginx.org/InstallOptions)

### 3.启动Nginx

检查PATH环境变量

<pre># ~/.bash_profile export PATH=/usr/local/bin:/usr/local/sbin:$PATH</pre>

启动Nginx

<pre>sudo nginx</pre>

需要停止Nginx的时候运行

<pre>sudo nginx -s stop</pre>

### 4.配置自启动

创建文件 <span style="color: #ff0000;">/System/Library/LaunchDaemons/nginx.plist</span>

<pre>&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd"&gt;
&lt;plist version="1.0"&gt;
&lt;dict&gt;
  &lt;key&gt;Label&lt;/key&gt;
  &lt;string&gt;nginx&lt;/string&gt;
  &lt;key&gt;KeepAlive&lt;/key&gt;
  &lt;true/&gt;
  &lt;key&gt;Program&lt;/key&gt;
  &lt;string&gt;/usr/local/bin/nginx&lt;/string&gt;
  &lt;key&gt;RunAtLoad&lt;/key&gt;
  &lt;true/&gt;
&lt;/dict&gt;
&lt;/plist&gt;
</pre>

载入自启动文件

<pre>launchctl load -w /System/Library/LaunchDaemons/nginx.plist</pre>