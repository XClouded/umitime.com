title: Delete SVN Folders
tags:
  - solution
  - delete svn
  - 删除svn
  - 批量删除SVN
date: 2010-11-15 12:26:04
---

复制以下内容到一个文本文件，然后改后缀名为.reg，双击导入注册表，之后在有SVN控制的文件夹右键 -&gt; Delete SVN Folders

Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Folder\shell\DeleteSVN]

@=&#8221;Delete SVN Folders&#8221;

[HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Folder\shell\DeleteSVN\command]

@=&#8221;cmd.exe /c \&#8221;TITLE Removing SVN Folders in %1 &amp;&amp; COLOR 9A &amp;&amp; FOR /r \&#8221;%1\&#8221; %%f IN (.svn) DO RD /s /q \&#8221;%%f\&#8221; \&#8221;&#8221;