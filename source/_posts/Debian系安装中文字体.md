title: Debian系安装中文字体
tags:
  - linux
date: 2011-09-15 15:50:24
---

基于Debian的linux系，比如ubuntu安装方法是一样的，来源于： http://hi.baidu.com/spiritualcity/blog/item/96369c2afa8740fde6cd40d2.html

我在ubuntu也是这么安装，ok，是为了解决 使用xvfb+CutyCapt实现网页抓图功能 遇到的中文乱码问题。

拷贝Windows下的字体:

simfang.ttf 仿宋体

simhei.ttf 黑体

simkai.ttf 楷体

simsun.ttf 宋体和新宋体，原文件名simsun.ttc

tahoma.ttf tahoma字体

tahomabd.ttf tahoma字体的粗体形式

verdana.ttf verdana字体

verdanab.ttf verdana字体的粗体形式

verdanai.ttf verdana字体的斜体形式

verdanaz.ttf verdana字体的粗体＋斜体形式

譬如到/home/rob/tools/fonts

cd /usr/share/fonts

ln -s /home/rob/tools/fonts xpfonts

cd xpfonts

mkfontscale

mkfontdir

这样作和把字体拷贝到/usr/share/fonts的一个目录下的效果是一样的。后面的两个mkfont命令是生成xpfonts目录下所包含的字体的索引信息。然后运行fc-cache命令更新字体缓存：

fc-cache