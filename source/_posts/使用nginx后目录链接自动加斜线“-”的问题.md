title: 使用nginx后目录链接自动加斜线“/”的问题
tags:
  - 自然
  - nginx目录访问问题
  - nginx链接自动加/
  - WordPress
date: 2010-10-16 15:16:38
---

开始是用的Apache，出现了性能问题，于是采用了[lnmp一键安装包](http://lnmp.org/)，转为nginx服务器。

之后WordPress出现了一些问题，比如，偶现在用的模板前端页面不提供登录入口的，偶手写URL访问登陆页面，当偶访问http://www.cssor.com/wp-admin （最后不加/），进入页面后左侧的菜单链接都错了，比如文章下的编辑，点击edit.php链接，进入了cssor.com/edit.php ,而不是cssor.com/wp-admin/edit.php。

wp-admin本身是目录，由于没有自动加/，造成相对路径的链接都出错，无法访问。

Google搜索之，找到方法，来源于：http://www.vpser.net/manage/nginx-auto-add-slash.html

> 最近lnmp一键安装包的部分用户反映在访问http://www.lnmp.org/phpmyadmin时，因为域名结尾没有加斜线”/”而造成无法访问。
> 
> 原来使用的自动添加斜线”/”的解决方法,通过查询Nginx wiki找到了更好的解决方法:
> 
> 在nginx.conf中
> 
> http{
> 
> &#8230;
> 
> server_name_in_redirect off;        #自动添加斜线”/”语句
> 
> &#8230;
> 
> }
> 
> Nginx wiki 官网介绍:http://wiki.nginx.org/NginxHttpCoreModule#server_name_in_redirect

配置文件目录是：/usr/local/nginx/conf/nginx.conf，原来的配置文件是没有server_name_in_redirect off;这句话的，需要添加，而不是修改。off是开启自动加，on是关闭自动加，注意不能聪明的以为是on。

2010年10月16日21:55:58，注：改了以后发现不是很好使，后台登陆是自动加了，但是偶的demo却不会自动加。。而且致命的是，虽然vpser那篇回复中说不影响wp的伪静态，但是偶设置的固定链接%postname%.html全部404了，于是又改了回来。 据可靠消息，目前的最新版nginx已经优化了这个问题，所以只要是最新版nginx，是不会有这个问题了。

第二种解决方案，针对WordPress，官方给的方法：http://techtitbits.com/2010/08/wordpress-permalinks-in-nginx/

编辑该域名的虚拟主机配置文件，找到location并添加try_files $uri $uri/ /index.php?q=$uri&amp;$args;，修改后的内容如下：

<pre class="brush:python">location / {
	        root   /var/www/nginx-default;
	        index  index.php index.html index.htm;
	        try_files $uri $uri/ /index.php?q=$uri&amp;$args;
	}</pre>