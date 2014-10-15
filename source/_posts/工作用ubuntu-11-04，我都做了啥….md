title: 工作用ubuntu 11.04，我都做了啥…
tags:
  - linux
date: 2011-06-09 10:23:11
---

### 1、RabbitVCS : TortoiseSVN 的替代者（来自http://wowubuntu.com/rabbitvcs.html）

TortoiseSVN 是 Windows 平台上版本控制程序 Subversion 的前端客户端，而 RabbitVCS 就是 Linux 平台下 TortoiseSVN 的最佳替代者。

RabbitVCS 是一款 Linux 平台上的版本控制前端程序，使用 Python 技术构建。可以与文件管理器 Nautilus 紧密整合，支持 Subversion (SVN) , Git 版本控制系统，未来还将支持 Mercurial 。

项目主页： http://rabbitvcs.org/

＃ Ubuntu 用户安装（与 Nautilus 整合）, 其它发行版请到这里下载。

sudo add-apt-repository ppa:rabbitvcs/ppa

sudo apt-get update

sudo apt-get install rabbitvcs-core rabbitvcs-nautilus rabbitvcs-cli

附加： 安装 RabbitVCS Gedit 扩展

sudo apt-get install rabbitvcs-gedit

修复无法显示图标的问题

gconftool-2 &#8211;set /desktop/gnome/interface/menus_have_icons &#8211;type bool true

最后输入以下命令重启 Nautilus 就可以使用 RabbitVCS 了。

nautilus -q

<span style="color: #008000;">PS. 据使用的同事说用起来会卡， 我先试试再说了。<span id="more-268"></span> </span>

### 2、安装lamp及phpmyadmin （来自 http://www.hackourlife.com/install-apache-mysql-php-phpmyadmin-lamp-on-ubuntu-11-04-or-mint-linux-10/）

先安装taskel

sudo apt-get install tasksel

然后

sudo tasksel install lamp-server

会进入下载，下载完成后需要填写mysql的root密码，然后完成安装。

下面会推荐一些软件包安装，我装了：

> To make MySQL, php and apache talk among themselves, you would have to install php5-mysql, below I am listing some
> 
> more useful packages that you might want to install
> 
> sudo apt-get install php5-mysql php5-curl php5-gd php5-idn php-pear php5-imagick php5-imap php5-mcrypt php5-memcache php5-ming php5-ps php5-pspell php5-recode php5-snmp php5-sqlite php5-tidy php5-xmlrpc php5-xsl php5-common

最后安装phpmyadmin:

sudo apt-get install phpmyadmin

&nbsp;

### 3、安装gvim（来自 http://www.kukaka.org/home/showonews/404）

文中说了一个问题，就不要重蹈覆泽了，从源码编译安装吧。

首先下载源码版本：

> 到www.vim.org上下载一份vim-7.3.tar.bz2
> 
> 开始编译，编译前当然先把需要的开发库libncurses5-dev安装一下：sudo apt-get install libncurses5-dev。
> 
> 然后解压进入vim/src，
> 
> 开始配置： ./configure &#8211;with-features=huge &#8211;enable-pythoninterp=yes &#8211;enable-gui=gnome2 &#8211;enable-cscope &#8211;enable-fontset &#8211;enable-perlinterp &#8211;enable-rubyinterp &#8211;with-python-config-dir=/usr/lib/python2.6/config
> 
> 编译和安装：make &amp;&amp; sudo make install
> 
> 这样一来vim就安装完了，然后再安装gvim：sudo apt-get install vim-gnome，大功告成。

### 4、安装浏览器Opera，flash插件

先去 http://www.adobe.com/go/getflash 下载 .tar.gz for linux ，直接找对应的64位版本 。

tar xvf install_flash_player_10_linux.tar.gz

cd install_flash_player_10_linux

sudo cp libflashplayer.so /usr/lib/mozilla/plugins/

opera原来是共用FF的，在  ~/.opera/pluginpaht.ini 中有 /usr/lib/mozilla/plugins=1 这行，没有可以自行添加。重启 opera 就OK。