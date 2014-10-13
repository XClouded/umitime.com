title: 配置Mac os终端下vi的配色
tags:
  - vim
  - mac os vim
date: 2014-09-18 16:16:41
---

Mac下的vim/vi home目录为：/usr/share/vim.

切到该目录下，执行sudo vi vimrc ,加入我以前的配置内容，参见：

[https://raw.githubusercontent.com/tofishes/Vim/master/_vimrc](https://raw.githubusercontent.com/tofishes/Vim/master/_vimrc)

然后切换目录到配色： cd vim73/colors

然后下载以前用的一个配色方案到该目录：

<pre>
wget https://raw.githubusercontent.com/tofishes/Vim/master/vimfiles/colors/molokai.vim
</pre>

至此就OK了。