title: Ubuntu使用xvfb+CutyCapt实现网页抓图功能
tags:
  - linux
date: 2011-09-15 16:02:11
---

来源于：    http://phpbob.blog.51cto.com/636017/663505

需要安装的软件包：

第一步安装，这些都是ubuntu上安装的方法。

<pre class="brush:shell">sudo apt-get update 
sudo apt-get -y install build-essential 
sudo apt-get install xvfb 
sudo apt-get install xfs xfonts-scalable xfonts-100dpi 
sudo apt-get install libgl1-mesa-dri 
sudo apt-get install subversion libqt4-webkit libqt4-dev g++</pre>

<span style="color: #008000;">Mark一下，本人在执行 sudo apt-get install libgl1-mesa-dri时候总是失败，故跳过了，依然安装成功。其中最后一句的subversoin我给去掉了，因为服务器中已经安装过了。</span>

第二步:

<pre class="brush:shell">svn co https://cutycapt.svn.sourceforge.net/svnroot/cutycapt</pre>

第三步：

<pre class="brush:shell">cd cutycapt/CutyCapt 
qmake 
make</pre>

第四步：命令行测试

<pre class="brush:shell">xvfb-run --server-args="-screen 0, 1024x768x24" ./CutyCapt --url=http://www.google.com --out=example.png</pre>

基本就ok。碰到中文乱码问题（必须的。。。），安装中文字体即可解决：    http://www.cssor.com/debian-coset-install-chinese-fonts.html

&nbsp;

另外一些参考：

http://blog.saymoon.com/2009/11/take-snapshot-in-linux-command-line/

安装中文字体：http://hi.baidu.com/spiritualcity/blog/item/96369c2afa8740fde6cd40d2.html

Linux中文内码控制方案：http://zhcon.sourceforge.net/index_cn.html （这个没用上）