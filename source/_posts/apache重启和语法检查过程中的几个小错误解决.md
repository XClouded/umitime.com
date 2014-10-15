title: apache重启和语法检查过程中的几个小错误解决
tags:
  - solution
  - 拓展
date: 2011-09-07 16:45:47
---

<span style="color: #008000;">来源于： http://blog.csdn.net/xuyaqun/article/details/6524108</span>

<span style="color: #008000;">我是碰到了最后一个端口443被多个虚拟主机共用的问题，环境是ubuntu。</span>

<span style="color: #000000;">重启apache时出现如下提示，虽然不影响apache的正常运行，但看着就别扭，还是解决掉吧：

apache2: Could not reliably determine the server’s fully qualified domain name, using 127.0.0.1 for ServerName

[Fri Jun 03 08:42:38 2011] [warn] _default_ VirtualHost overlap on port 443, the first has precedenceapache语法检查时出现如下提示：

# apache2 -t

apache2: bad user name ${APACHE_RUN_USER}

1、# vi /etc/apache2/apache2.conf

127 User www-data

128 Group www-data

2、apache2: Could not reliably determine the server’s fully qualified domain name, using 127.0.0.1 for ServerName

解决方法：

# vi /etc/apache2/httpd.conf

添加

ServerName localhost

3、[Fri Jun 03 08:42:38 2011] [warn] _default_ VirtualHost overlap on port 443, the first has precedence

解决方法：

# vi /etc/apache2/ports.conf

添加

NameVirtualHost *:443

最后重启apache即可。

</span>

<span style="color: #000000;">sudo /etc/init.d/apache2 restart </span>