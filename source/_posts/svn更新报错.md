title: svn更新报错
tags:
  - 服务器
  - native encoding
  - svn
  - svn error
date: 2011-12-27 16:00:10
---

svn: Can&#8217;t convert string from &#8216;UTF-8&#8242; to native encoding:

svn: xmas-bottom-logo &#8211; ?\229?\137?\175?\230?\156?\172.png

Linux终端字符显示问题，执行命令：

export LANG=&#8221;zh_CN.UTF-8&#8243;

然后再次更新即可，果然发现中文出现在名字中：

svn up &#8211;force

A xmas-bottom-logo &#8211; 副本.png

Updated to revision 1276.