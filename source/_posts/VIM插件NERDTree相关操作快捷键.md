title: VIM插件NERDTree相关操作快捷键
tags:
  - vim
  - NERDTree
date: 2012-08-06 14:07:31
---

来源于：http://blog.sina.com.cn/s/blog_662f20a10100nrun.html

shift+i 显示隐藏文件

let NERDChristmasTree=1 &#8221; 让树更好看,我是没看出来

let NERDTreeCaseSensitiveSort=1 &#8221; 让文件排列更有序

let NERDTreeChDirMode=1 &#8221; 改变tree目录的同时改变工程的目录

let NERDTreeHijackNetrw=1 &#8221; 当输入 [:e filename]不再显示netrw,而是显示nerdtree

let NERDTreeWinPos=&#8221;right&#8221;

let NERDTreeBookmarksFile=&#8217;/Users/admin/.vim/NERDTreeBookmarks&#8217;

以下是帮助文档中的使用介绍

o&#8230;&#8230;.Open files, directories and bookmarks&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;..|NERDTree-o|&#8230;&#8230;.打开文件

go&#8230;&#8230;Open selected file, but leave cursor in the NERDTree&#8230;..|NERDTree-go|&#8230;&#8230;.打开文件,光标 不变位置

t&#8230;&#8230;.Open selected node/bookmark in a new tab&#8230;&#8230;&#8230;&#8230;&#8230;..|NERDTree-t|&#8230;&#8230;.新标签打开

T&#8230;&#8230;.Same as &#8216;t&#8217; but keep the focus on the current tab&#8230;&#8230;..|NERDTree-T|&#8230;&#8230;.同上但留在当前

<span id="more-906"></span>

i&#8230;&#8230;.Open selected file in a split window&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;|NERDTree-i|&#8230;&#8230;.分裂窗口打开文件

gi&#8230;&#8230;Same as i, but leave the cursor on the NERDTree&#8230;&#8230;&#8230;.|NERDTree-gi|&#8230;&#8230;.同上留在当前

s&#8230;&#8230;.Open selected file in a new vsplit&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;..|NERDTree-s|&#8230;&#8230;.垂直分裂窗口

gs&#8230;&#8230;Same as s, but leave the cursor on the NERDTree&#8230;&#8230;&#8230;.|NERDTree-gs|&#8230;&#8230;.

O&#8230;&#8230;.Recursively open the selected directory&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;|NERDTree-O|&#8230;&#8230;.

x&#8230;&#8230;.Close the current nodes parent&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;|NERDTree-x|&#8230;&#8230;.收起目录

X&#8230;&#8230;.Recursively close all children of the current node&#8230;&#8230;.|NERDTree-X|&#8230;&#8230;.收起所有

e&#8230;&#8230;.Edit the current dif&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;.|NERDTree-e|

D&#8230;&#8230;.Delete the current bookmark &#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;..|NERDTree-D|&#8230;&#8230;.上出标签

P&#8230;&#8230;.Jump to the root node&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;|NERDTree-P|&#8230;&#8230;.跳到目录顶端

p&#8230;&#8230;.Jump to current nodes parent&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;..|NERDTree-p|&#8230;&#8230;.跳到父目录

K&#8230;&#8230;.Jump up inside directories at the current tree depth&#8230;..|NERDTree-K|&#8230;&#8230;.跳到第一个目录

J&#8230;&#8230;.Jump down inside directories at the current tree depth&#8230;|NERDTree-J|&#8230;&#8230;.跳到最后一个文件

&lt;C-J&gt;&#8230;Jump down to the next sibling of the current directory&#8230;|NERDTree-C-J|

&lt;C-K&gt;&#8230;Jump up to the previous sibling of the current directory.|NERDTree-C-K|

C&#8230;&#8230;.Change the tree root to the selected dir&#8230;&#8230;&#8230;&#8230;&#8230;..|NERDTree-C|&#8230;&#8230;.改变当前目录

u&#8230;&#8230;.Move the tree root up one directory&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;.|NERDTree-u|&#8230;&#8230;.回上一级目录

U&#8230;&#8230;.Same as &#8216;u&#8217; except the old root node is left open&#8230;&#8230;..|NERDTree-U|&#8230;&#8230;.同上并保留显示文件结构

r&#8230;&#8230;.Recursively refresh the current directory&#8230;&#8230;&#8230;&#8230;&#8230;.|NERDTree-r|&#8230;&#8230;.刷新目录

R&#8230;&#8230;.Recursively refresh the current root&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;|NERDTree-R|&#8230;&#8230;.刷新当前根目录

m&#8230;&#8230;.Display the NERD tree menu&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;.|NERDTree-m|&#8230;&#8230;.显示树菜单

cd&#8230;&#8230;Change the CWD to the dir of the selected node&#8230;&#8230;&#8230;..|NERDTree-cd|

I&#8230;&#8230;.Toggle whether hidden files displayed&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;..|NERDTree-I|

f&#8230;&#8230;.Toggle whether the file filters are used&#8230;&#8230;&#8230;&#8230;&#8230;..|NERDTree-f|

F&#8230;&#8230;.Toggle whether files are displayed&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;..|NERDTree-F|

B&#8230;&#8230;.Toggle whether the bookmark table is displayed&#8230;&#8230;&#8230;..|NERDTree-B|

q&#8230;&#8230;.Close the NERDTree window&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;..|NERDTree-q|

A&#8230;&#8230;.Zoom (maximize/minimize) the NERDTree window&#8230;&#8230;&#8230;&#8230;.|NERDTree-A|

?&#8230;&#8230;.Toggle the display of the quick help&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;|NERDTree-?