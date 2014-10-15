title: Mac OSX下手动安装MongoDB
tags:
  - 服务器
  - mongodb
  - osx mongodb
date: 2014-07-29 12:42:52
---

1、从官网下载osx系统的mongodb包到目录/home/soft，并解压。

<pre>cd /home/soft
curl -O http://downloads.mongodb.org/osx/mongodb-osx-x86_64-2.6.3.tgz
tar -zxvf mongodb-osx-x86_64-2.6.3.tgz
</pre>

2、建立一个软连接，并创建一个数据库目录

<pre>ln -s ~/home/soft/mongodb-osx-x86_64-2.6.3 /usr/local/mongodb
mkdir -p /usr/local/mongodb/database
</pre>

<span id="more-1169"></span>

3、创建mongod启动的配置文件，并手动启动mongodb试试看。

<pre>vi /usr/local/mongodb/mongod.conf
</pre>

输入以下内容后保存退出

<pre># MongoDB数据库存储目录
dbpath = /usr/local/mongodb/database
# 日志目录
# logpath = /usr/local/var/log/mongodb.log
# 绑定ip，限定只允许本地连接
bind_ip = localhost
# 开启journaling
journal = true
# 开启REST
rest = true
# 启用更小的文件存储
# (可以节省开发环境下的空间)
smallfiles = true
</pre>

手动启动mongodb试试是否成功：

<pre>cd /usr/local/mongodb
./bin/mongod --config=./mongod.conf
</pre>

看输出，应该OK了。control+c可以终止。

4、把mongodb相关命令加入shell

如果是用BASH，执行

<pre>echo 'export PATH=/usr/local/mongodb/bin:$PATH' &gt;&gt; ~/.bash_profile
source ~/.bash_profile
</pre>

我用的是oh-my-zsh， 修改~/.zshrc文件

<pre>vi ~/.zshrc
</pre>

加入一行

<pre>export PATH=/usr/local/mongodb/bin:$PATH
</pre>

保存并退出后，重新载入

<pre>source ~/.zshrc
#然后就可以直接用mongod启动了
mongod --config=/usr/local/mongodb/mongod.conf
</pre>

5、每次手动都要加入长长的参数，不想写成一个mongo_start.sh，干脆配置为随系统自动启动。

<pre>vi ~/Library/LaunchAgents/org.mongodb.mongod.plist
</pre>

输入

<pre>&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd"&gt;
&lt;plist version="1.0"&gt;
&lt;dict&gt;
 &lt;key&gt;Label&lt;/key&gt;
 &lt;string&gt;org.mongodb.mongod&lt;/string&gt;

 &lt;key&gt;ProgramArguments&lt;/key&gt;
 &lt;array&gt;
 &lt;string&gt;/usr/local/mongodb/bin/mongod&lt;/string&gt;
 &lt;string&gt;--config=/usr/local/mongodb/mongod.conf&lt;/string&gt;
 &lt;/array&gt;

 &lt;key&gt;RunAtLoad&lt;/key&gt;
 &lt;true/&gt;
 &lt;key&gt;KeepAlive&lt;/key&gt;
 &lt;true/&gt;
&lt;/dict&gt;
&lt;/plist&gt;</pre>

然后注册到启动项

<pre>launchctl load -w ~/Library/LaunchAgents/org.mongodb.mongod.plist
</pre>

从启动项取消，使用

<pre>launchctl unload -w ~/Library/LaunchAgents/org.mongodb.mongod.plist
</pre>

完工！

参考资料：

http://mac-dev-env.patrickbougie.com/mongodb/

http://docs.mongodb.org/manual/tutorial/install-mongodb-on-os-x/