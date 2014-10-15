title: Ubuntu下共享软件Samba的安装与配置
tags:
  - linux
  - 服务器
  - linux共享windows
  - samba
  - ubuntu
date: 2012-02-28 14:47:58
---

<span style="color: #008000;">来源于： http://blog.csdn.net/liufei_learning/article/details/6895127</span><span style="color: #008000;"> </span>

可以用于linux与linux、windows主机间的文件共享。

一.Samba的安装:

<pre class="brush:shell">$ sudo apt-get insall samba
$ sudo apt-get install smbfs</pre>

二. 创建共享目录:

<pre class="brush:shell">$ mkdir /home/work/samba_share

#若不更改权限，windows下不能写入（适用于只读共享）!!!
$ sudo chmod 777 /home/work/samba_share</pre>

三. 修改Samba配置文件:

<pre class="brush:shell"># 备份samba的配置文件
$ cp /etc/samba/smb.conf/etc/samba/smb.conf.old
$ sudo vi /etc/samba/smb.conf</pre>

在文件最后添加如下内容

<span id="more-604"></span>

<span style="color: #008000;">[root]</span>

<span style="color: #008000;">comment = Root Directories</span>

<span style="color: #008000;">browseable = yes</span>

<span style="color: #008000;">writeable =yes</span>

<span style="color: #008000;">path= /</span>

<span style="color: #008000;">valid users = smb</span>

以上为修改的内容，简单说明一点path是共享的路径， /就共享根目录了，共享给smb用户，就意味着等下登陆的时候必须使用smb为用户名

下面添加系统用户

<pre class="brush:shell">$ useradd smb
$ smbpasswd smb
#或者smbpasswd -a smb输入密码重复2次，比如说密码是123456。</pre>

重启samba服务

$ /etc/init.d/smbd restart

查看ubuntu的ip地址，$ ifconfig，比如说地址是192.168.1.2

把windows中的防火墙也关掉。

在运行中输入  \\192.168.1.2\root  或者输入\\192.168.1.2\后面出现一个对话框要求输入用户名密码

用户名就是smb密码就是123456，就可以看到文件夹了，就可以在linux与windows直接共享文件了