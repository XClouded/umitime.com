title: 我用到的vim插件记录
tags:
  - vim
date: 2011-09-15 16:26:39
---

插件vba格式的安装方法是 用vim打开这个vba文件，然后执行

:so %

就开始安装。

<span style="color: #008000;">HTML5语法高亮及自动补全：http://www.vim.org/scripts/script.php?script_id=3236</span>

> <span style="color: #000000;">自动缩进的$vim/vimfiles/indent/html.vim文件 中需要添加以下标签定义，不然没有缩进</span>
> 
> <span style="color: #000000;">
> 
> </span><span style="color: #000000;">call &lt;SID&gt;HtmlIndentPush(&#8216;p&#8217;)
> 
> call &lt;SID&gt;HtmlIndentPush(&#8216;li&#8217;)
> 
> call &lt;SID&gt;HtmlIndentPush(&#8216;dt&#8217;)
> 
> call &lt;SID&gt;HtmlIndentPush(&#8216;dd&#8217;) </span>

<span style="color: #008000;">快速从模板建立文件：  http://www.gracecode.com/archives/2414/</span>

<span style="color: #008000;">自动触发提示，而不需要按组合键：  http://www.vim.org/scripts/script.php?script_id=1879</span>

> 使用该插件 定制PHP的全能提示触发命令
> 
> php 中 一般是会在 &#8220;$&#8221;, &#8220;-&gt;&#8221;, &#8220;::&#8221; 后需要出现自动补全，在 .vimrc 中加入以下代码：
> 
> if !exists(&#8216;g:AutoComplPop_Behavior&#8217;)
> 
> let g:AutoComplPop_Behavior = {}
> 
> let g:AutoComplPop_Behavior['php'] = []
> 
> call add(g:AutoComplPop_Behavior['php'], {
> 
> \   &#8216;command&#8217;   : &#8220;\&lt;C-x&gt;\&lt;C-o&gt;&#8221;,
> 
> \   &#8216;pattern&#8217;   : printf(&#8216;\(-&gt;\|::\|\$\)\k\{%d,}$&#8217;, 0),
> 
> \   &#8216;repeat&#8217;    : 0,
> 
> \})
> 
> endif
> 
> 这样就可以了。