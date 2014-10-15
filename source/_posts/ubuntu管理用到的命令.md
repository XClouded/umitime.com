title: ubuntu管理用到的命令
tags:
  - linux
date: 2011-11-02 09:49:26
---

1、查看磁盘空间 (http://hi.baidu.com/tlexander/blog/item/0e5fdb971167a47c55fb969a.html)

<pre class="brush:shell">df -hl #查看磁盘剩余空间
df -h  #查看每个根路径的分区大小
du -sh #[目录名] 返回该目录的大小
du -sm #[文件夹] 返回该文件夹总M数</pre>

2、ubuntu查看进程端口号及运行的程序 （http://guotieyun.blog.163.com/blog/static/10702102920104921610449/）

<pre class="brush:shell">netstat -atunp | more
#根据端口号查pid，比如知道80，查apache是否运行中
lsof -i:631</pre>

3、为网卡配置静态IP地址 （http://hi.baidu.com/beloving/blog/item/9eff51437411fc149213c6d4.html）

sudo vi /etc/network/interfaces，并用下面的行来替换有关eth0的行:

# The primary network interface

auto eth0

iface eth0 inet static

address 192.168.3.90

gateway 192.168.3.1

netmask 255.255.255.0

#network 192.168.3.0

#broadcast 192.168.3.255

用下面的命令使网络设置生效:

sudo /etc/init.d/networking restart

4、管理软件，除了apt-get，还可以aptitude

<pre class="brush:shell">aptitude update	        #更新可用的包列表
aptitude upgrade	#升级可用的包
aptitude dist-upgrade	#将系统升级到新的发行版
aptitude install pkgname	#安装包
aptitude remove pkgname	#删除包
aptitude purge pkgname	#删除包及其配置文件
aptitude search string	#搜索包
aptitude show pkgname	3显示包的详细信息
aptitude clean	        #删除下载的包文件
aptitude autoclean	#仅删除过期的包文件</pre>

5、设置主机名称(hostname)

<pre class="brush:shell">sudo /bin/hostname #查看当前主机的主机名称
sudo /bin/hostname newname  #设置当前主机的主机名称</pre>

6、