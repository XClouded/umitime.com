title: vim设置自动会话与htmlindent插件的不和谐
tags:
  - vim
date: 2012-08-09 13:41:04
---

虽然不是很大问题，但是每次按Enter也是很不爽的事情。。。

首先，设置了vim的session（会话）自动保存与自动载入，实现代码如下，来源于网络：

<span id="more-919"></span>

<pre>"""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
"保存，载入session
fu! SaveSess()
	set sessionoptions-=curdir
	set sessionoptions+=sesdir
	set sessionoptions+=slash
	set sessionoptions+=unix
    "set sessionoptions-=options
    execute 'mksession! $vim/vim-session.vim'
    "execute 'wviminfo! $currProject.viminfo'
endfunction

fu! RestoreSess()
	execute 'source $vim/vim-session.vim'
	"execute 'rviminfo $currProject.viminfo'
endfunction

"最后处理session
autocmd VimLeave * call SaveSess()
autocmd VimEnter * call RestoreSess()</pre>

然后也使用了vimfiles/indent/html.vim处理html的缩进。

出现的问题就是vim启动后，自动打开上次会话保存的html文件时，总是会先提示：

<pre>"aa.html" [unix] 545L, 12388C
Sourcing html indent
**Press ENTER or type command to continue**</pre>

如果html文件很多，就需要回车很多次。。。

Google很多次无果，后来在一个Vimer朋友的提醒下，找到了原因，使用 :scriptnames可以看到插件和会话文件的加载顺序，结果发现，会话文件始终是先于html.vim插件加载。经过测试后，确定了问题确实是出现在这里： 加载顺序导致的。

后来又找如何处理加载顺序，无果。没办法，最后解决方案是：改自动载入会话为手动载入，配置如下：

<pre>"去掉原来的自动载入
"autocmd VimEnter * call RestoreSess()
let mapleader=","
map rr :source $vim/vim-session.vim</pre>

这样就可以每次打开vim， 用 ,rr 载入上次会话。