title: MAC下ssh免密码登录远程服务器
tags:
  - 服务器
  - ssh
date: 2014-01-02 20:54:40
---

工作上换了个MAC AIR本本，工作环境要恶搞一阵子了。首先是和远程Linux服务器SSH自动登录设置，同时此设置OK以后，Git也可以免密码执行了。

第一步：生成密钥。在终端下执行命令：

<pre>ssh-keygen -t rsa</pre>

一路回车，各种提示按默认不要改，等待执行完毕。然后执行：

<pre>ls ~/.ssh
#可以看到两个密钥文件：id_rsa（私钥） id_rsa.pub（公钥）</pre>

第二步：放置公钥到Linux服务器。使用scp命令：

<pre>scp ~/.ssh/id_rsa.pub cssor@cssor.com:/home/cssor/.ssh/</pre>

然后把id_rsa.pub的内容加入到服务器端的默认验证文件authorized_keys，执行：

<pre>#先登录到远程服务器
cd ~/.ssh
cat -n /home/cssor/.ssh/id_rsa.pub &gt;&gt; authorized_keys #将公钥内容加入到authorized_keys文件，没有则新建一个就行
</pre>

第三步：配置本地ssh config文件。执行：

<pre>vi ~/.ssh/config</pre>

加入以下内容：

<pre>Host cssor_server  #别名，域名缩写
    HostName cssor.com  #完整的域名
    User cssor  #登录该域名使用的账号名
    IdentityFile ~/.ssh/id_rsa #私钥文件的路径</pre>

经过以上操作以后，已经大功告成，感谢一同事帮忙，虽然他用的是Ubuntu系统。

使用ssh cssor_server或ssh cssor.com就可以直接登录到远程服务器了。并且如果git服务器也是搭建在同一个Linux主机上，本地执行git pull等也不需要密码再次登录了。

以上比较要注意的是： 公钥要放在登录服务器所用的账号的家目录下，比如你用 abc登录远程服务器，就要把公钥 放到  /home/abc/.ssh/下， authorized_keys文件也是在这个目录下。我曾因为放错账号目录，导致失败。

附ssh config一个资料：http://nerderati.com/2011/03/simplify-your-life-with-an-ssh-config-file/