title: Mac OSX下配置nginx和php
tags:
  - 服务器
  - mac php
  - nginx php
  - php-fpm
date: 2014-06-03 19:11:00
---

首先安装nginx, 参见：http://cssor.com/mac-nginx.html。

其次需要HomeBrew, 按照官网说明安装：http://brew.sh.

然后安装PHP，以下的命令在终端执行：

<span id="more-1094"></span>

更新下brew的库，加入php支持：

<pre>brew tap homebrew/dupes
brew tap josegonzalez/homebrew-php
</pre>

然后执行：

<pre>
# 需要mysql模块，就把--without-mysql改为--with-mysql
brew install --without-apache --with-fpm --without-mysql php55
</pre>

执行上面的命令后，brew就开始下载php源码并编译安装，大概几分钟时间。

安装完成后，配置php/bin到终端环境(未验证，貌似跟mac本身自带的php有冲突？)：

<pre>
#这一步成不成功没关系，这步的目的只是让终端可以执行PHP命令，不影响web服务器使用
echo 'export PATH="$(brew --prefix josegonzalez/php/php55)/bin:$PATH"' >> ~/.bash_profile
</pre>

设置随系统自动启动：

<pre>
mkdir -p ~/Library/LaunchAgents
#注意文件homebrew.mxcl.php55.plist的路径，需要根据实际情况改成自己的目录和文件名
cp /usr/local/Cellar/php55/5.5.13/homebrew.mxcl.php55.plist ~/Library/LaunchAgents/
</pre>

上面只是设置了自启动，但是还没真正启动，所以执行以下命令手动启动PHP-FPM：

<pre>
launchctl load -w ~/Library/LaunchAgents/homebrew.mxcl.php55.plist
</pre>

php的配置和启动工作已经结束了，然后验证一下，执行

<pre>lsof -Pni4 | grep LISTEN | grep php
</pre>

如果输出以下类似内容，那就表示成功了，否则。。。自找原因吧：

<pre>
php-fpm   69659  frdmn    6u  IPv4 0x8d8ebe505a1ae01      0t0  TCP 127.0.0.1:9000 (LISTEN)
php-fpm   69660  frdmn    0u  IPv4 0x8d8ebe505a1ae01      0t0  TCP 127.0.0.1:9000 (LISTEN)
php-fpm   69661  frdmn    0u  IPv4 0x8d8ebe505a1ae01      0t0  TCP 127.0.0.1:9000 (LISTEN)
php-fpm   69662  frdmn    0u  IPv4 0x8d8ebe505a1ae01      0t0  TCP 127.0.0.1:9000 (LISTEN)    
</pre>

最后就是配置nginx虚拟机：

<pre>
server {
    listen  80;
    server_name  php.cssor.com;
    root   /var/www;

    location / {
        index  index.html index.shtml;
    }
    #配置后缀为.php的就交给php引擎处理
    #如果需要整站都托管给php，就把location ~ \.php$ 替换为location / ,同时上面的location / 配置就不需要了
    location ~ \.php$ {
        fastcgi_pass   127.0.0.1:9000;
        fastcgi_index  index.php;
    	fastcgi_param  SCRIPT_FILENAME    $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }
}
</pre>

以上参考了：http://blog.frd.mn/install-nginx-php-fpm-mysql-and-phpmyadmin-on-os-x-mavericks-using-homebrew/