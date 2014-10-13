title: Mac下配置iterm2和oh-my-zsh
tags:
  - linux
  - iterm2
  - zsh
date: 2014-09-19 17:45:43
---

晒个图先：

[![iterm2-theme](http://cssor.com/wp-content/uploads/2014/09/iterm2-theme-300x187.png)](http://cssor.com/wp-content/uploads/2014/09/iterm2-theme.png)

<span id="more-1248"></span>

首先安装oh-my-zsh，参照[官网](http://ohmyz.sh)方法：

<pre>curl -L http://install.ohmyz.sh | sh
</pre>

或者：

<pre>wget --no-check-certificate http://install.ohmyz.sh -O - | sh
</pre>

然后按照[https://raw.githubusercontent.com/tofishes/iterm2-zsh/master/_zshrc](https://raw.githubusercontent.com/tofishes/iterm2-zsh/master/_zshrc)文件内容来配置zsh：

<pre>vi ~/.zshrc
#参照上述链接内容来做修改，主题用nebirhos
</pre>

接下来去[官网下载Iterm2](http://iterm2.com).

安装Iterm完毕，启动后进入设置窗口，导入配色主题（[点此下载配置主题Monokai-Soda.itermcolors](https://raw.githubusercontent.com/tofishes/iterm2-zsh/master/Monokai-Soda.itermcolors)）：

[![iterm2-theme-setting](http://cssor.com/wp-content/uploads/2014/09/iterm2-theme-setting-300x187.png)](http://cssor.com/wp-content/uploads/2014/09/iterm2-theme-setting.png)

同样的配置窗口，切换到window项，配置背景图（[点此下载背景图](https://github.com/tofishes/iterm2-zsh/blob/master/calligraphy05.jpg)）：

[![iterm2-background](http://cssor.com/wp-content/uploads/2014/09/iterm2-background.jpg)](http://cssor.com/wp-content/uploads/2014/09/iterm2-background.jpg)

ok,结束。

打包资源： https://github.com/tofishes/iterm2-zsh