title: ubuntu mongodb安装及备份与恢复
tags:
  - database
  - install mongodb
  - mongodb
  - 备份mongodb
  - 安装mongodb
date: 2011-12-13 16:40:18
---

使用命令

apt-key adv &#8211;keyserver keyserver.ubuntu.com &#8211;recv 7F0CEB10

然后编辑源：

vi /etc/apt/sources.list

增加一行：

deb http://downloads-distro.mongodb.org/repo/ubuntu-upstart dist 10gen

然后更新并安装：

apt-get update

apt-get install mongodb-10gen

这样安装下来就是最新稳定版本。

导出数据库：

mongodump -h localhost &#8211;db database_name -o /home/backup

注意-o参数是目录，然后恢复数据：

mongorestore -h localhost &#8211;db database_name &#8211;directoryperdb /home/backup

由于导出后的数据库是多个.bson文件 ，可以打包方便保存或传输：

tar -zcvf  ./dump-name.tar.gz  /home/backup