title: Ubuntu server php config及配置mongodb驱动
tags:
  - linux
  - solution
  - mongodb
  - php mongoDB driver
  - php.ini
date: 2011-10-17 15:33:23
---

ubuntu server下的php配置应该改什么文件？ php.ini ？也对，也不对。

php.ini位于 /etc/php5/apache2/目录下 ，是php总配置文件，但是在ubuntu下一般不要去修改这个文件。

ubuntu下php的配置应该创建在 /etc/php5/conf.d/目录下。

<span id="more-465"></span>

最近在部署 一个php web应用，使用到mongoDB。访问后发生以下错误：

Fatal error: Class &#8216;Mongo&#8217; not found in /var/www/&#8230;&#8230;

经google了解这是没有为php安装mongo驱动 造成的。所以按照网上查询结果安装了一下mongo驱动。

最后说是要在php.ini中加入一句： extension=mongo.so。

但是vi php.ini后，直接搜 extension是木有任何结果的，我就很纳闷，php绝对不至于不添加extension，于是猜测应该不是直接在php.ini中配置（用多了ubuntu会有种感觉）。

后来在ubuntu中文社区的wiki上找到答案：[ http://wiki.ubuntu.org.cn/LAMP_服务器安装配置#.E9.85.8D.E7.BD.AEPHP5](http://wiki.ubuntu.org.cn/LAMP_服务器安装配置#.E9.85.8D.E7.BD.AEPHP5 "LAMP_服务器安装配置")

进入/etc/php5/conf.d/以后，果然发现有很多配置文件，这里的配置文件应该是被php自动加载的。那么配置mongo.so就简单了：

<pre class="brush:shell">cd /etc/php5/conf.d
sudo vi mongo.ini
#加入以下内容,保存后退出
#extension=mongo.so</pre>

然后重启apache2，问题解决。

附上php添加mongoDB驱动的方法，来源于： [http://www.iblue.cc/2011/06/mac-os-x和ubuntu下安装mongodb-的php驱动/](http://www.iblue.cc/2011/06/mac-os-x和ubuntu下安装mongodb-的php驱动/ "mac-os-x和ubuntu下安装mongodb-的php驱动")

> <pre class="brush:shell">#在 http://pecl.php.net/package/mongo  选择你需要的驱动版本进行下载> 
> 
> tar zxf mongo-1.2.6.tgz> 
> cd mongo-1.2.6> 
> phpize> 
> #Configuring for:> 
> #PHP Api Version:         20090626> 
> #Zend Module Api No:      20090626> 
> #Zend Extension Api No:   220090626> 
> ./configure --enable-mongo> 
> make install> 
> 
> #这样mongo的php扩展就安装好了，最后更改php.ini 文件 在里面加上  extension=mongo.so</pre>