title: VPS折腾手记
tags:
  - 自然
date: 2010-08-07 20:13:45
---

从淘宝买了vps，昨日晚上账号到手，折腾到凌晨2点，之后今天又继续折腾到现在，总算服务器+blog+ftp折腾完毕。

Apache2主要是设置虚拟主机，因为本服务承载了两个人的blog；blog则是权限问题（文件上传需要读写） ，ftp是完全安装配置。

主机是Ubuntu10.04系统。默认安装有Apache2，Mysql，其他则无。使用Xshell登录SSH。

配置Apache2的虚拟主机，修改/ect/apache2/httpd.conf (默认是空文件的)，直接添加

NameVirtualHost ip

&lt;VirtualHost ip&gt;

ServerName www.cssor.com

DocumentRoot /docroot

&lt;Directory &#8220;/docroot&#8221;&gt;

Options FollowSymLinks

AllowOverride None

Order allow,deny

Allow from all

&lt;/Directory&gt;

&lt;/VirtualHost&gt;

&lt;VirtualHost ip&gt;

ServerName 域名2

DocumentRoot /docroot2

&lt;Directory &#8220;/docroot2&#8243;&gt;

Options FollowSymLinks

AllowOverride None

Order allow,deny

Allow from all

&lt;/Directory&gt;

&lt;/VirtualHost&gt;

这里也折腾了不少，开始是没配置directory标签，出了个警告，赶紧加上，接着是没配置 NameVirtualHost，又是警告，

然后NameVirtualHost偶配置了多个，还是警告，才知道NameVirtualHost配置一个就够了，后面跟着ip就行了。这样两个域名都ok了。

Apache2的管理命令：

$root: /etc/init.d/apache2 restart  //重启

$root: /etc/init.d/apache2 stop //停止，换成start是启动

编辑配置文件是vi /etc/apache2/httpd.conf

以上Apache是配置ok了。然后为了方便上传，安装了ftp服务器（如果是一个人，可以用Xftp以Sftp的方式管理），使用vsftpd。

$root: apt-get install vsftpd

一路安装下来，最后是配置：

vi  /etc/vsftpd.conf

去掉了一些原有语句的注释：

#本地用户可用

local_enable=YES

#加点banner提示

ftpd_banner=Hello~~

#不让匿名访问

anonymous_enable=NO

#按推荐的来

local_umask=022

#启动chroot列表(Change root)

chroot_list_enable=YES

#锁定ftp根目录，不让向上访问

local_root=/home

#使用用户列表文件

userlist_enable=YES

#用户列表没有定义的用户不能访问ftp

userlist_deny=NO

write_enable=YES #开启可写，这个配置 郁闷了，刚配置时没有加上，导致ftp可以访问了，但是愣是不能上传，又折腾一会儿，看到有资料提及此配置。

上面开启了用户列表文件，那么就需要配置一下了：

vi /etc/vsftpd.user_list

加入几个用户名，比如

user1

user2 #每行一个用户名

:wq保存退出即可。然后在系统中添加用户: useradd user1 ; useradd user2； 分别设置密码： passwd user1; passwd user2；。

接下来配置blog，上传wp程序到Apache配置的虚拟主机对应的目录去。然后访问域名，出现安装页面。这里忘了要说一下，VPS系统默认安装有Mysql数据库，用户名是root，默认密码空

blog安装出现wp-config.php无法写入问题，及之后博客上传主题文件（忘了这里也是需要ftp的，wp为虾米这么弄，http上传下载也可以撒）等无法写入，连ftp也是无法写入的。

于是权限的设置郁闷了一下。这里不得不说，有两个命令，有些相似，不常用就弄混了。

文件所属命令 chown，和 权限设置命令 chmod ，（change owner, change mode）

可以想象到了，偶把chomd用成了chown，结果发现依然存在权限问题。。。郁闷啊。新手，直接根目录统统的 777 权限。

chown -R 777 /home/user1 #杯具的只是改了所属用户而已。777可以替换为用户名，比如 chown -R user1 /home/user1, -R会递归子文件

chmod u+w+r+x /home/user1 # 这才真正的解决权限问题，u 与文件属主拥有一样的权限，刚刚chown了，那么u指所有用户（777）或user1, +赋予权限，-删除权限，w写，r读，x执行

另外apt-get install phpmyadmin用于管理mysql 。

简略手记完毕。

补充：真伤心，修改固定链接以后，全部404，遂搜索半天，虚拟主机里配置Directory里的 AllowOverride All，.htaccess文件权限修改了，不行，去看Apache2的配置，哎，Ubuntu自带的Apache2配置太乱了，找不到地方，后来才看到/etc/apache2/mods-available/rewrite.load文件里一句话：LoadModule rewrite_module /usr/lib/apache2/modules/mod_rewrite.so

而/etc/apache2/apache2.conf里没有包含mods-available文件夹下的任何文件，只好把这句话又写入到httpd.conf，

这样总算是好了。