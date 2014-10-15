title: 使WordPress安装主题插件不再需要提供FTP信息
tags:
  - solution
  - WordPress
date: 2011-10-12 11:50:48
---

来源于： http://www.zivee.cn/2010/03/wordpress-plugin-and-upgrade-need-ftp-info/

修改wp-config.php，添加下面两行代码即可，测试有效：

<pre class="brush:php">//wp-config.php底部追加下面代码
//其定义值可以为： 'direct', 'ssh', 'ftpext' (ftp方式)or 'ftpsockets'(Socket extension)
define('FS_METHOD', 'direct');
//修改目录权限为0751， 默认是0755
define('FS_CHMOD_DIR',0751);

//在一台服务器测试有效，但是隔了几天后在一个vps测试却无效了，提示
//在wp-content/upgrade/目录无法写入，所以手动创建了upgrade目录，
//并给以权限可以写入
sudo mkdir upgrade;
sudo chmod +w upgrade;
</pre>