title: LNMP一键安装包正常使用期间出现nginx 502 Bad Gateway错误
tags:
  - solution
  - 服务器
  - bad gateway
  - lnmp
  - nginx 502
date: 2012-02-15 10:35:24
---

最近站点隔一两天就会出现nginx 502 Bad Gateway错误，无法访问，以前都好好的，莫非是流量上来了的缘故？

因为站点是用LNMP一键安装包搭建的环境，所以google了下这方面的情况，得到两个可能的解决方案，试试看了。

最后验证第三种方案相当靠谱，用了以后就没有出现过502现象。

<span id="more-578"></span>

### 第一个是官方的FAQ( http://bbs.vpser.net/thread-1144-1-1.html )：

第一种原因：目前lnmp一键安装包比较多的问题就是502 Bad Gateway，大部分情况下原因是在安装php前，脚本中某些lib包可能没有安装上，造成php没有编译安装成功。

解决方法：

可以尝试根据lnmp一键安装包中的脚本手动安装一下，看看是什么错误导致的，在网上搜索一下，或者把错误信息发上来。我们给你分析一下错误原因。

第二种原因：

在php.ini里，eaccelerator配置项一定要放在Zend Optimizer配置之前，否则也可能引起502 Bad Gateway

第三种原因：

在安装好使用过程中出现502问题，一般是因为默认php-cgi进程是5个，可能因为phpcgi进程不够用而造成502，需要修改/usr/local/php/etc/php-fpm.conf 将其中的max_children值适当增加。

也有可能是max_requests值不够用。

第四种原因：

php执行超时，修改/usr/local/php/etc/php.ini 将max_execution_time 改为300

第五种原因：

磁盘空间不足，如mysql日志占用大量空间

第六种原因：

查看php-cgi进程是否在运行

&nbsp;

### 第二个是非官方的，但是谈到了和官方第三种原因一样的解决方案，所以先试了下这个方案：

http://www.redicecn.com/html/Linux/20111226/358.html，http://blog.s135.com/post/361/

使用 netstat -anpo | grep &#8220;php-cgi&#8221; | wc -l 命令可以查看当前实际的FastCGI进程数，如果该数字接近预设的值（预设值可以在/usr/local/php/etc/php-fpm.conf中查看&lt;value name=&#8221;max_children&#8221;&gt;5&lt;/value&gt;）则需要增大该预设值。

编辑/usr/local/php/etc/php-fpm.conf，将&lt;value name=&#8221;max_children&#8221;&gt;5&lt;/value&gt;根据情况修改为较大的值，然后重启LNMP，502问题没有再出现。

&nbsp;

暂时还没看到效果如何，已经改成了max_children = 20， 期待效果。

改成20就死定了，SSH的命令行就执行不了，内存吃空了，后来在vps控制面板 重启了vps，又把max_children改小，为10才可以正常ssh执行命令了。

### 接着尝试了第三个方案，改监听地址：

参考自 http://itfis.com/Internet/212.html， http://www.5dw.org/Archives/20110406476.html。

具体步骤：

1.修改nginx.conf

nginx安装目录：/usr/local/nginx/conf/ ，打开nginx.conf，查找到：

fastcgi_pass  unix:/tmp/php-cgi.sock;

改为：

fastcgi_pass  127.0.0.1:9000;

2.修改php-fpm.conf

php安装目录：，/usr/local/php/etc/，打开php-fpm.conf

查找到第26行：

&lt;value name=&#8221;listen_address&#8221;&gt;/tmp/php-cgi.sock&lt;/value&gt;

改为

&lt;value name=&#8221;listen_address&#8221;&gt;127.0.0.1:9000&lt;/value&gt;

3.依次重启Nginx和php-fpm

nginx 重启：

/usr/local/nginx/sbin/nginx -t

/usr/local/nginx/sbin/nginx -s reload

php-fpm重启：

/usr/local/php/sbin/php-fpm restart

如果大家想用/root/vhost.sh生成出来的新站点，都自动生成这种端口监听信息的话，就vi /root/vhost.sh，修改148行内容即可。

<span style="color: #008000;">** 试用第三种改监听端口的方案，已经看到效果，以前隔1、2天就502的情况在改后连续一周都没有再出现过，的确有效。**</span>

&nbsp;

&nbsp;