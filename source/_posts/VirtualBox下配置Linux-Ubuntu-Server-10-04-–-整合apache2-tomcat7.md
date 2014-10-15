title: VirtualBox下配置Linux Ubuntu Server 10.04 – 整合apache2 tomcat7
tags:
  - linux
date: 2011-09-26 15:07:32
---

首先安装ubuntu server，一路安装下来很容易。（apache查看版本号： apache2ctl -v）

##### 1、安装openSSH，由于在安装过程中没有选择任何网络服务，这里手动安装。

直接 sudo apt-get install openssh-server，客服端 sudo apt-get install openssh-client 。一般客户端自带，不用再安装，执行 ssh 命令就知道了。

可以通过编辑 /etc/ssh/sshd_config 文件来配置 OpenSSH

sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.original

sudo chmod a-w /etc/ssh/sshd_config.original

vi /etc/ssh/sshd_config

配置完成后重起：

sudo /etc/init.d/ssh restart

确认sshserver是否启动，执行ps -e |grep ssh

如果只有ssh-agent或什么也没看到，那ssh-server还没有启动，如果看到sshd那说明ssh-server已经启动了。

连接SSH服务： ssh username:password@host-domain

<span id="more-423"></span>

##### 2、Vbox虚拟机网络连通问题。

Vbox默认使用NAT连接，这样外网就无法访问虚拟机，但是虚拟机可以访问外网。ubuntu下看ip使用 ifconfig ，宿主主机确实ping不通虚拟机。

将网络连接方式改为： Bridged Adapter， 然后执行 sudo /etc/init.d/networking restart

##### 3、安装增强工具

<pre class="brush:shell">1、首先，选择设备-&gt;安装增强功能 

2、然后安装以下的包： 

sudo  aptitude install build-essential linux-headers-$(uname -r) -y 

3、挂载cd-rom。 

sudo  mount /dev/cdrom /mnt/ 

4、安装增强包 

sudo  /mnt/VBoxLinuxAdditions-x86.run 

5、卸载cdrom 

sudo umount /mnt/ 

6、共享windows中的文件，比如在virtualbox中设置的共享空间叫vbshare，于是在ubuntu中输入如下命令 

sudo mount -t vboxsf vbshare /mnt</pre>

##### 4、命令行下中文乱码。

经过以上操作后发现命令的回显很多乱码 ，一猜就知道，大概是因为安装的时候界面选择了中文，而命令行下没有中文包支持，干脆系统语言改回默认英文，google “修改ubuntu server为英文”得到结果。

<pre class="brush:shell">#修改配置文件
sudo vi /etc/default/locale
#中文设置为：
LANG="zh_CN.UTF-8"
LANGUAGE="zh_CN:zh"
#修改为：
LANG="en_US.UTF-8"
LANGUAGE="en_US:en"
#重启之后就好了。

#另修改时区：
vi /etc/timezone
#输入：
Asia/Shanghai
#也可以使用以下命令打开交互式界面来更改系统时区:
sudo dpkg-reconfigure tzdata

#后用tab补全命令时候发现一个问题，总提示：-bash: warning: setlocale: LC_CTYPE: cannot change locale (en_US.UTF-8)
#这时候需执行：
sudo locale-gen en_US.UTF-8
sudo update-locale LANG=en_US.UTF-8
#然后重启系统解决。</pre>

##### 5、安装jdk6

ubuntu下用apt-get只能安装到open-jdk，无法安装到sun-jdk了。

按照这篇来安装：    http://diegobenna.blogspot.com/2011/01/install-tomcat-7-in-ubuntu-1010.html

在http://www.oracle.com/technetwork/java/javase/downloads/index.html 下载到  jdk-6u27-linux-x64.bin， 并SFTP到ubuntu server的目录/home/tofishes。

注意jdk版本，原文是安装x86 32位系统，我安装的是x64系统。

首先赋予 jdk-6u27-linux-x64.bin 执行权限:

<pre class="brush:shell">cd /home/tofishes
sudo chmod +x jdk-6u27-linux-x64.bin</pre>

接着执行安装：

sudo ./jdk-6u27-linux-x64.bin

一路刷屏了不少解包信息 ，最后回车Done。

<pre class="brush:shell">#最后查看目录如下
~$ ls
jdk1.6.0_27  jdk-6u27-linux-x64.bin

#然后移动到更常用的应用目录
sudo mv jdk1.6.0_27/ /usr/local</pre>

接下来激活新安装的java版本为默认版本，设置环境变量：

<pre class="brush:shell">#设置新安装的java为可选之一（因为可能已经安装有其他版本，比如jdk1.5）
$ sudo update-alternatives --install "/usr/bin/java" "java" "/usr/local/jdk1.6.0_27/bin/java" 1
#回显
#update-alternatives: using /usr/local/jdk1.6.0_27/bin/java to provide /usr/bin/java (java) in auto mode.

#设置为默认使用的版本，命令无回显
$ sudo update-alternatives --set java /usr/local/jdk1.6.0_27/bin/java

#使用同样的步骤设置javac命令(后来遇到用jar命令，同样的方法设置一下)
sudo update-alternatives --install "/usr/bin/javac" "javac" "/usr/local/jdk1.6.0_27/bin/javac" 1
sudo update-alternatives --set javac /usr/local/jdk1.6.0_27/bin/javac

#ok了，执行命令查看版本
$ java -version

#java version "1.6.0_27"
#Java(TM) SE Runtime Environment (build 1.6.0_27-b07)
#Java HotSpot(TM) 64-Bit Server VM (build 20.2-b06, mixed mode)

$ javac -version

#javac 1.6.0_27</pre>

如果想换回其他版本的jdk，可以用以下命令配置（我没试过，因为没有其他版本可以试）：

sudo update-alternatives &#8211;config java

sudo update-alternatives &#8211;config javac

如果要设置JAVA_HONE, JRE_HOME为环境变量，需要：

<pre class="brush:shell"># 编辑环境变量
sudo vi /etc/environment

# 修改或新添加以下内容
JAVA_HOME = "/usr/local/jdk1.6.0_27/bin/" (新添加)
JRE_HOME = "/usr/local/jdk1.6.0_27/jre" (新添加)
PATH ="...(其他已有变量不要动，后面追加):$JAVA_HOME:$JRE_HOME (修改)</pre>

JDK到此安装结束。

##### 6、安装tomcat7。

按照这一篇来： http://diegobenna.blogspot.com/2011/01/install-tomcat-7-in-ubuntu-1010.html

<pre class="brush:shell"># 先下载一个tomcat7：
cd /home/tofishes
wget http://mirror.olnevhost.net/pub/apache/tomcat/tomcat-7/v7.0.21/bin/apache-tomcat-7.0.21.tar.gz
# 解压
tar xvzf apache-tomcat-7.0.21.tar.gz
# 移动
sudo mv apache-tomcat-7.0.21/ /usr/share/tomcat7
# 安装jdk已经设置好环境变量，tomcat一般会自动认出，我的是自动认出了，但是原作者出了意外，在此也贴上解决方法（必杀技）：
sudo vi /usr/share/tomcat7/bin/catalina.sh

# 将文件头部加入环境变量，看起来就是:
#!/bin/sh
 JAVA_HOME="/usr/local/jdk1.6.0_23"
 JRE_HOME="/usr/local/jdk1.6.0_23/jre"
# Licensed to the Apache Software Foundation (ASF)...
#...</pre>

接下来配置tomcat的user文件：

<pre class="brush:shell">sudo vi /usr/share/tomcat7/conf/tomcat-users.xml</pre>

添加如下内容：

<pre class="brush:xml">&lt;?xml version='1.0' encoding='utf-8'?&gt;

&lt;tomcat-users&gt;
    &lt;role rolename="manager-gui"/&gt;
    &lt;role rolename="manager-script"/&gt;
    &lt;role rolename="manager"/&gt;
    &lt;role rolename="admin-gui"/&gt;
    &lt;role rolename="admin-script"/&gt;
    &lt;role rolename="admin"/&gt;

    &lt;user username="usuario" password="contrasena" roles="manager-gui,admin-gui,manager,admin,manager-script,admin-script"/&gt;
&lt;/tomcat-users&gt;</pre>

现在可以启动tomcat试试了：

<pre class="brush:shell"># 启动命令
sudo /usr/share/tomcat7/bin/startup.sh

# 启动命令
sudo /usr/share/tomcat7/bin/shutdown.sh</pre>

然后访问 http://localhost:8080，能够看到首页了。

现在实现一下自动启动tomcat及定义更方便的启动停止重启：

<pre class="brush:shell">sudo vi /etc/init.d/tomcat7

# 加入以下内容

 # Tomcat auto-start
 #
 # description: Auto-starts tomcat
 # processname: tomcat
 # pidfile: /var/run/tomcat.pid

 case $1 in
 start)
 sh /usr/share/tomcat7/bin/startup.sh
 ;;
 stop)
 sh /usr/share/tomcat7/bin/shutdown.sh
 ;;
 restart)
 sh /usr/share/tomcat7/bin/shutdown.sh
 sh /usr/share/tomcat7/bin/startup.sh
 ;;
 esac
 exit 0</pre>

保存后，赋予此文件可执行权限：

<pre class="brush:shell">sudo chmod 755 /etc/init.d/tomcat7</pre>

然后将这个脚本软链接到系统启动文件夹中，就可以随系统自动启动了：

<pre class="brush:shell">sudo ln -s /etc/init.d/tomcat7 /etc/rc1.d/K99tomcat
sudo ln -s /etc/init.d/tomcat7 /etc/rc2.d/S99tomcat</pre>

同时快捷管理tomcat可以用以下命令：

<pre class="brush:shell"># 重启
sudo /etc/init.d/tomcat7 restart
# 启动
sudo /etc/init.d/tomcat7 start
#　停止
sudo /etc/init.d/tomcat7 stop</pre>

tomcat配置到此结束。

##### 7、整合apache2与tomcat。（暂时已解决）

jk方式整合始终无法转发成功，原因待定。这里使用proxy_http方法转发成功。

首先启用相关模块：

<pre class="brush:shell">sudo a2enmod proxy
sudo a2enmod proxy_http
sudo /etc/init.d/apache2 reload
#助记：a2enmod 意思为 apache2 enable module</pre>

修改proxy.conf:

<pre class="brush:shell">sudo vi /etc/apache2/mods-enabled/proxy.conf
#将里面的Deny from all 改为Allow from all</pre>

然后配置一个虚拟主机：

<pre class="brush:shell">cd /etc/apache2/sites-available
sudo vi cc-com #假设站点域名为 cc.com</pre>

配置内容如下：

<pre class="brush:xml">&lt;VirtualHost *:80&gt;
        ServerAdmin cc@cc.com
        ServerName cc.com
        ServerAlias  www.cc.com

        &lt;!-- ProxyPreserveHost的作用貌似是自动对目录加/结束，看这里 http://blog.csdn.net/paulluo0739/article/details/3404114 --&gt;
        ProxyPreserveHost On
        ProxyRequests Off

        &lt;!-- cc.com:8080 需要在tomcat server.xml里配置 --&gt;
        ProxyPass / http://cc.com:8080/
        ProxyPassReverse / http://cc.com:8080/

        ErrorLog /var/log/apache2/cc.com.log
        CustomLog /var/log/apache2/cc.com.custom.log common
&lt;/VirtualHost&gt;</pre>

启用这个配置：

<pre class="brush:shell">sudo a2ensite cc-com</pre>

最后在tomcat server.xml中配置一个Host:

<pre class="brush:xml">&lt;Host name="cc.com" appBase="/home/cc" uppackWARs="true" autoDeploy="false"&gt;
     &lt;Context docBase="WebRoot" path="" reloadable="true" /&gt;
&lt;/Host&gt;</pre>

注意因为是假域名，需要修改hosts文件：

sudo vi /etc/environment

添加：

127.0.0.1   cc.com

&nbsp;

重启各服务器：

<pre class="brush:shell">sudo /etc/init.d/tomcat7 restart
sudo /etc/init.d/apache2 reload
sudo /etc/init.d/apache2 restart</pre>

在浏览器访问 cc.com 可以看到自己的jsp应用了。

———————————————————— 分割线（线下为mod_jk整合方式，总失败，暂留着纪念，再研究） ————————————————————

原文： http://diegobenna.blogspot.com/2011/01/connect-tomcat-7-with-apache2-modjk-and.html

&nbsp;

sudo apt-get install libapache2-mod-jk

sudo vi /etc/apache2/workers.properties

后来发现tomcat启动后，log文件发现以下错误：

wc_create_worker::jk_worker.c (139): Unknown worker type aj113 for worker dev

[Fri Jun 11 10:57:26.414 2010] [4853:2129983296] [error]

这里查到一个解决方法https://bugs.launchpad.net/ubuntu/+source/libapache-mod-jk/+bug/592576

原来是copy原作者的workers.properties粘贴到vi的时候，每行 前面产生一个空格，去掉每行前面的空格，解决问题。

&nbsp;

sudo vi /etc/apache2/httpd.conf

sudo a2dissite default

sudo /etc/init.d/apache2 reload

* Reloading web server config apache2                                                      apache2: Could not reliably determine the server&#8217;s fully qualified domain name, using 127.0.1.1 for ServerName

sudo /etc/init.d/apache2 restart

&nbsp;

NameVirtualHost xjn.cn:80

&lt;VirtualHost xjn.cn:80&gt;

ServerName xjn.cn

JKMount /* worker

JKMount / worker

RewriteEngine on

RewriteRule ^/$ / [R=permanent]

RewriteRule ^/(.*)$ ajp://localhost:8009/$1 [P]

&lt;/VirtualHost&gt;