title: Vim多窗口及设置工作路径相关命令
tags:
  - vim
date: 2011-09-15 16:24:18
---

来源于：http://dlutyezhang.blogcn.com/articles/vim-多窗口切换.html

#### Vim 多窗口切换

vim 从 vim7 开始加入了多标签切换的功能， 相当于多窗口.

之前的版本虽然也有多文件编辑功能， 但是总之不如这个方便啦。

用法

:tabnew [++opt选项] ［＋cmd］ 文件

建立对指定文件新的tab

:tabc       关闭当前的tab

:tabo       关闭所有其他的tab

:tabs       查看所有打开的tab

:tabp      前一个

:tabn      后一个

标准模式下：

gt , gT 可以直接在tab之间切换。

#### VIM 自动改变工作路径

cd: 改变vim的当前工作路径

lcd： 改变当前窗口的工作路径

pwd: 查看当前的工作路径

set autochdir: 自动设当前编辑的文件所在目录为当前工作路径 ，这个可以加入到_vimrc文件中去