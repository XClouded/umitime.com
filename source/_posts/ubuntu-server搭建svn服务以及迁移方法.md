title: ubuntu server搭建svn服务以及迁移方法
tags:
  - linux
  - 服务器
date: 2011-09-14 16:18:49
---

### 搭建SVN服务

采用apache+svn，http访问方式。

先确定安装了apache，没有安装则用以下命令：

<pre class="brush:shell">sudo apt-get install apache2</pre>

然后安装subversion及svn-apache连接库：

<pre class="brush:shell">sudo apt-get install subversion
sudo apt-get install libapache2-svn</pre>

<span id="more-382"></span>增加svn用户组，用于管理svn，并加入到www-data的apache组：

&nbsp;

<pre class="brush:shell">sudo addgroup subversion
sudo usermod -G subversion -a www-data</pre>

然后可以创建svn项目仓库了：

<pre class="brush:shell"># 一般可以将项目仓库建立到/home/svn目录
# 假设项目名为myproject，先建立空目录
$ sudo mkdir /home/svn
$ cd /home/svn
$ sudo mkdir myproject
$ sudo chown -R root:subversion myproject

#创建为svn项目，使用以下命令
$ sudo svnadmin create /home/svn/myproject
# 然后才赋予组成员对所有新加入文件仓库的文件拥有相应的权限：
# 如果命令顺序搞错，可能会报错，请参照官方wiki
$ sudo chmod -R g+rws myproject</pre>

最后是配置apache，用http协议访问svn服务：

<pre class="brush:shell"># 在安装了libapache2-svn时会自动生成该文件
$ sudo vi /etc/apache2/mods-available/dav_svn.conf</pre>

编辑dav_svn.conf配置文件，内有注释，可以去掉前面的#使语句生效，最后内容大概如下即可：

<pre class="brush:shell">&lt;Location /svn&gt;  #/svn表示http://hostname/svn/myproject
  DAV svn
  SVNParentPath /home/svn #配置仓库父目录
  AuthType Basic
  AuthName "ToFishes Project Svn"
  AuthUserFile /etc/subversion/passwd  #svn用户文件
  AuthzSVNAccessFile /etc/subversion/authz #授权访问文件
  Require valid-user
&lt;/Location&gt;</pre>

svn用户文件/etc/subversion/passwd 和 授权访问文件/etc/subversion/authz是不存在的，需要手动创建。

svn用户文件可以通过命令创建：

<pre class="brush:shell">#首次创建需要加-c选项，同时可以增加一个svn用户
#执行该命令会提示为新用户user_name设置密码
sudo htpasswd -c /etc/subversion/passwd user_name

#以后添加新用户，需要去掉-c选项，否则以前的用户就被覆灭了
sudo htpasswd /etc/subversion/passwd new_name</pre>

用户是可以添加了，同时需要管理授权，用于多个svn项目，划分不同的用户组：

用vi /etc/subversion/authz编辑授权文件，无文件则编辑并保存后会自动创建，无需担心。

授权文件内容格式如下：

<pre class="brush:shell">#用户组指令
[groups]
#格式为  组名 = 用户名1，用户名2
group1 = user1, user2
group2 = user1, user3
#other groups...
#格式为  svn项目的仓库名:对应目录
[svn_repository_name:/]
#设置组的权限，r=read, w=write
@group1 = rw
[svn_repository_name2:/web/css]
@group2 = rw</pre>

改完最后重启apache :   sudo /etc/init.d/apache2 restart。这样工作就全部完成了。

其中访问地址是什么呢，只要当前服务器已经绑定了一个域名，并且在apache中启用了此域名的虚拟主机。

比如能正常访问 http://www.cssor.com/, 那么svn项目的访问地址就是 http://www.cssor.com/svn/项目名。

### 迁移SVN仓库

最近服务器硬盘坏道导致频繁自动关机，所以需要迁移SVN到新的服务器去。

用以上同样的方法在新服务搭建好SVN，接着导出原svn仓库资料，再导入到新svn仓库即可。

1\. 在老服务器进行导出操作：

$ svnadmin dump 原先的repos的目录路径（/repository/directory） &gt; dumpfile.svndump

2\. 在新服务器将dumpfile.svndump导入到新的repository 目录中。

$ svnadmin load 新建的repos的目录路径（/repository/directory） &lt; dumpfile.svndump

如果项目仓库比较多，附上一个批处理导出命令，导入的改改就行了：

<pre class="brush:shell">#!/bin/bash
cd /home/svn
filelist=`ls` #符号·不是单引号，而是位于键盘感叹号左侧
for fname in $filelist
do
  svnadmin dump $fname &gt; /home/svndump/${fname}.svndump
  #echo $fname
  #最好是root用户执行，否则要sudo了
done</pre>

那么本地项目svn的检出及更新地址就需要变更了， 使用TortoiseSVN工具的右键找到Relocate菜单就可以了，而使用命令的话如下：

<pre class="brush:shell">#在项目目录查看下仓库地址
$ svn info
#回显中的URL项就是旧版地址oldpath
#然后切换到新版地址newpath即可
$ svn switch --relocate http://oldpath http://newpath
#查看信息确认下
$ svn info</pre>

参考内容：

*   http://wiki.ubuntu.org.cn/SubVersion
*   https://help.ubuntu.com/community/Subversion
*   http://ihacklog.com/server/ubuntu-server/ubuntu-svn-setup.html