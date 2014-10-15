title: Gvim windows下窗口透明
tags:
  - vim
date: 2011-08-26 17:42:58
---

<span style="color: #008000;">来源于：http://hi.baidu.com/idea_star_/blog/item/6133ac0df7acf93c6a60fb7c.html</span>

在linux下可以实现窗口透明，最近在windows下折腾vim，一直想尝试将窗口设置为透明。于是google之，找到了<span class="Apple-style-span" style="widows: 2; text-transform: none; text-indent: 0px; letter-spacing: normal; border-collapse: separate; font: medium Simsun; white-space: normal; orphans: 2; color: #000000; word-spacing: 0px;"><span class="Apple-style-span" style="line-height: 20px; font-family: arial, helvetica, sans-serif, verdana, 宋体; font-size: 14px;">vimtweak.dll</span></span>

<span class="Apple-style-span" style="widows: 2; text-transform: none; text-indent: 0px; letter-spacing: normal; border-collapse: separate; font: medium Simsun; white-space: normal; orphans: 2; color: #000000; word-spacing: 0px;"><span class="Apple-style-span" style="line-height: 20px; font-family: arial, helvetica, sans-serif, verdana, 宋体; font-size: 14px;">下载地址：[http://www.vim.org/scripts/script.php?script_id=687](http://www.vim.org/scripts/script.php?script_id=687)</span></span>

<span class="Apple-style-span" style="widows: 2; text-transform: none; text-indent: 0px; letter-spacing: normal; border-collapse: separate; font: medium Simsun; white-space: normal; orphans: 2; color: #000000; word-spacing: 0px;"><span class="Apple-style-span" style="line-height: 20px; font-family: arial, helvetica, sans-serif, verdana, 宋体; font-size: 14px;">下载后把<span class="Apple-style-span" style="widows: 2; text-transform: none; text-indent: 0px; letter-spacing: normal; border-collapse: separate; font: medium Simsun; white-space: normal; orphans: 2; color: #000000; word-spacing: 0px;"><span class="Apple-style-span" style="line-height: 20px; font-family: arial, helvetica, sans-serif, verdana, 宋体; font-size: 14px;">vimtweak.dll放到gvim.exe的安装目录下。</span></span></span></span>

<span class="Apple-style-span" style="widows: 2; text-transform: none; text-indent: 0px; letter-spacing: normal; border-collapse: separate; font: medium Simsun; white-space: normal; orphans: 2; color: #000000; word-spacing: 0px;"><span class="Apple-style-span" style="line-height: 20px; font-family: arial, helvetica, sans-serif, verdana, 宋体; font-size: 14px;"><span class="Apple-style-span" style="widows: 2; text-transform: none; text-indent: 0px; letter-spacing: normal; border-collapse: separate; font: medium Simsun; white-space: normal; orphans: 2; color: #000000; word-spacing: 0px;"><span class="Apple-style-span" style="line-height: 20px; font-family: arial, helvetica, sans-serif, verdana, 宋体; font-size: 14px;">打开gvim，敲入下面命令来实现对应功能。窗口透明后面的参数可以是0~255.个人感觉大于200比较好。</span></span></span></span>

<span id="more-370"></span>

<span class="Apple-style-span" style="widows: 2; text-transform: none; text-indent: 0px; letter-spacing: normal; border-collapse: separate; font: medium Simsun; white-space: normal; orphans: 2; color: #000000; word-spacing: 0px;"><span class="Apple-style-span" style="line-height: 20px; font-family: arial, helvetica, sans-serif, verdana, 宋体; font-size: 14px;"><span class="Apple-style-span" style="widows: 2; text-transform: none; text-indent: 0px; letter-spacing: normal; border-collapse: separate; font: medium Simsun; white-space: normal; orphans: 2; color: #000000; word-spacing: 0px;"><span class="Apple-style-span" style="line-height: 20px; font-family: arial, helvetica, sans-serif, verdana, 宋体; font-size: 14px;">窗口透明：

:call libcallnr(&#8220;vimtweak.dll&#8221;, &#8220;SetAlpha&#8221;, 200)

reset alpha

:call libcallnr(&#8220;vimtweak.dll&#8221;, &#8220;SetAlpha&#8221;, 255)最大化窗口:

Enable

:call libcallnr(&#8220;vimtweak.dll&#8221;, &#8220;EnableMaximize&#8221;, 1)

Disable

:call libcallnr(&#8220;vimtweak.dll&#8221;, &#8220;EnableMaximize&#8221;, 0)窗口置顶:

Enable

:call libcallnr(&#8220;vimtweak.dll&#8221;, &#8220;EnableTopMost&#8221;, 1)

Disable

:call libcallnr(&#8220;vimtweak.dll&#8221;, &#8220;EnableTopMost&#8221;, 0)</span></span></span></span>

也可以在vimrc中加入如下内容

&#8220;Alpha Window

<span style="color: #000000;">map </span><span style="color: #008800;">&lt;leader&gt;</span><span style="color: #000000;">aw </span><span style="color: #666600;">:</span><span style="color: #000000;">call libcallnr</span><span style="color: #666600;">(</span><span style="color: #008800;">&#8220;vimtweak.dll&#8221;</span><span style="color: #666600;">,</span><span style="color: #008800;">&#8220;SetAlpha&#8221;</span><span style="color: #666600;">,</span><span style="color: #006666;">200</span><span style="color: #666600;">)&lt;</span><span style="color: #000000;">cr</span><span style="color: #666600;">&gt;</span><span style="color: #000000;">

map </span><span style="color: #008800;">&lt;leader&gt;</span><span style="color: #000000;">aW </span><span style="color: #666600;">:</span><span style="color: #000000;">call libcallnr</span><span style="color: #666600;">(</span><span style="color: #008800;">&#8220;vimtweak.dll&#8221;</span><span style="color: #666600;">,</span><span style="color: #008800;">&#8220;SetAlpha&#8221;</span><span style="color: #666600;">,</span><span style="color: #006666;">255</span><span style="color: #666600;">)&lt;</span><span style="color: #000000;">cr</span><span style="color: #666600;">&gt;</span>

&#8220;Maximized Window

<span style="color: #000000;">map </span><span style="color: #008800;">&lt;leader&gt;</span><span style="color: #000000;">mw </span><span style="color: #666600;">:</span><span style="color: #000000;">call libcallnr</span><span style="color: #666600;">(</span><span style="color: #008800;">&#8220;vimtweak.dll&#8221;</span><span style="color: #666600;">,</span><span style="color: #008800;">&#8220;EnableMaximize&#8221;</span><span style="color: #666600;">,</span><span style="color: #006666;">1</span><span style="color: #666600;">)&lt;</span><span style="color: #000000;">cr</span><span style="color: #666600;">&gt;</span><span style="color: #000000;">

map </span><span style="color: #008800;">&lt;leader&gt;</span><span style="color: #000000;">mW </span><span style="color: #666600;">:</span><span style="color: #000000;">call libcallnr</span><span style="color: #666600;">(</span><span style="color: #008800;">&#8220;vimtweak.dll&#8221;</span><span style="color: #666600;">,</span><span style="color: #008800;">&#8220;EnableMaximize&#8221;</span><span style="color: #666600;">,</span><span style="color: #006666;">0</span><span style="color: #666600;">)&lt;</span><span style="color: #000000;">cr</span><span style="color: #666600;">&gt;</span>

&#8220;TopMost Window

<span style="color: #000000;">map </span><span style="color: #008800;">&lt;leader&gt;</span><span style="color: #000000;">et </span><span style="color: #666600;">:</span><span style="color: #000000;">call libcallnr</span><span style="color: #666600;">(</span><span style="color: #008800;">&#8220;vimtweak.dll&#8221;</span><span style="color: #666600;">,</span><span style="color: #008800;">&#8220;EnableTopMost&#8221;</span><span style="color: #666600;">,</span><span style="color: #006666;">1</span><span style="color: #666600;">)&lt;</span><span style="color: #000000;">cr</span><span style="color: #666600;">&gt;</span><span style="color: #000000;">

map </span><span style="color: #008800;">&lt;leader&gt;</span><span style="color: #000000;">eT </span><span style="color: #666600;">:</span><span style="color: #000000;">call libcallnr</span><span style="color: #666600;">(</span><span style="color: #008800;">&#8220;vimtweak.dll&#8221;</span><span style="color: #666600;">,</span><span style="color: #008800;">&#8220;EnableTopMost&#8221;</span><span style="color: #666600;">,</span><span style="color: #006666;">0</span><span style="color: #666600;">)&lt;</span><span style="color: #000000;">cr</span><span style="color: #666600;">&gt;</span>

<span class="Apple-style-span" style="widows: 2; text-transform: none; text-indent: 0px; letter-spacing: normal; border-collapse: separate; font: medium Simsun; white-space: normal; orphans: 2; color: #000000; word-spacing: 0px;"><span class="Apple-style-span" style="line-height: 21px; font-family: Arial, Helvetica, Geneva, sans-serif; font-size: 14px;"><span style="font-size: 14px;">还有，vtano.ahk：GVIM+AHK实现顶层透明笔记本，链接：[http://xbeta.info/ahk-gvim-note.htm](http://xbeta.info/ahk-gvim-note.htm)</span></span></span>

<span style="margin: 0px; font-family: NSimsun; padding: 0px;"><span style="margin: 0px; color: #0000ff; padding: 0px;">1\. 基本信息</span>

<span style="margin: 0px; color: #0000ff; padding: 0px;">;      名称：vtano.ahk：GVIM+AHK实现顶层透明笔记本</span>

<span style="margin: 0px; color: #0000ff; padding: 0px;">;      作者：xbeta (善用佳软)</span>

<span style="margin: 0px; color: #0000ff; padding: 0px;">;      版本：v1.0  (2007-10-30)</span>

<span style="margin: 0px; color: #0000ff; padding: 0px;">;      网站：[http://blog.sina.com.cn/xbeta](http://blog.sina.com.cn/xbeta)</span>

<span style="margin: 0px; color: #0000ff; padding: 0px;">;      由来：经常在阅读文档时，希望有一个小巧的笔记工具，可以透明悬浮在顶层，随时键入文字，而不影响原来的阅读对象。由于我极喜欢GVIM，所以想到了用Auto Hotkey设置GVIM置顶、透明的办法。当然，GVIM自身有一个VimTweak扩展，可实现类似功能。但VimTweak的透明是背景与文字整体设定透明度，这样文字看起来不清晰。而AHK可以设定背景色完全透明（如果能设定透明度就更理想了），而文字正常显示，我认为这种效果更好一些。</span>

<span style="margin: 0px; color: #0000ff; padding: 0px;">;      适用对象：计算机上装有Auto Hotkey 和 GVIM，并且极其偏爱它们</span>

<span style="margin: 0px; color: #0000ff; padding: 0px;">;  2\. 使用方法</span>

<span style="margin: 0px; color: #0000ff; padding: 0px;">;      将此文件放在加入path变量的目录（如系统目录）下，运行 vtano.ahk 即可</span>

<span style="margin: 0px; color: #0000ff; padding: 0px;">;      或任意放一位置，然后建立xx.lnk，运行 xx 即可</span>

<span style="margin: 0px; color: #0000ff; padding: 0px;">;  3\. 说明</span>

<span style="margin: 0px; color: #0000ff; padding: 0px;">;      ① 如果你不用AHK，也可以把此文档编译为exe</span>

<span style="margin: 0px; color: #0000ff; padding: 0px;">;      ② 中间大段的send，也可以改写到vim脚本中</span>

<span style="margin: 0px; color: #0000ff; padding: 0px;">;      ③ 如果想Gvim的标题栏也透明，只须让标题栏颜色（windows样式）、gvim背景色、ahk设定的窗口透明色三色相同。</span><span style="margin: 0px; color: #804040; padding: 0px;">**SetTitleMatchMode**</span>,<span style="background-color: #f2f2f2; margin: 0px; padding: 0px;"><span style="margin: 0px; color: #ff00ff; padding: 0px;">2</span></span> <span style="margin: 0px; color: #0000ff; padding: 0px;">;设定ahk匹配窗口标题的模式</span>

<span style="margin: 0px; color: #804040; padding: 0px;">**run**</span> d:\Program Files\gvim\vim71\gvim.exe<span style="margin: 0px; color: #0000ff; padding: 0px;">;启动gvim</span>

<span style="margin: 0px; color: #804040; padding: 0px;">**winactivate**</span>, No Name<span style="margin: 0px; color: #0000ff; padding: 0px;">; 激活此窗口</span>

<span style="margin: 0px; color: #804040; padding: 0px;">**sleep**</span>,<span style="background-color: #f2f2f2; margin: 0px; padding: 0px;"><span style="margin: 0px; color: #ff00ff; padding: 0px;">500</span></span> <span style="margin: 0px; color: #0000ff; padding: 0px;">; 延时，确保</span><span style="margin: 0px; color: #804040; padding: 0px;">**send**</span> :hi clear Normal{enter}<span style="margin: 0px; color: #0000ff; padding: 0px;">;取消gvim的颜色</span>

<span style="margin: 0px; color: #804040; padding: 0px;">**send**</span> :hi clear{enter}<span style="margin: 0px; color: #0000ff; padding: 0px;">;取消gvim的颜色</span>

<span style="margin: 0px; color: #804040; padding: 0px;">**send**</span> :hi Normal guifg=red guibg=green{enter}<span style="margin: 0px; color: #0000ff; padding: 0px;">;设定为绿底红字</span>

<span style="margin: 0px; color: #804040; padding: 0px;">**send**</span> :e c:\WINDOWS\Temp\vtano.text{enter}<span style="margin: 0px; color: #0000ff; padding: 0px;">;打开笔记文件，后缀为text是防止txt有其他语法加亮</span></span>

<span style="margin: 0px; color: #804040; padding: 0px;">**sleep**</span>,<span style="background-color: #f2f2f2; margin: 0px; padding: 0px;"><span style="margin: 0px; color: #ff00ff; padding: 0px;">500</span></span>

<span style="margin: 0px; color: #804040; padding: 0px;">**winmove**</span>, vtano.text,,<span style="background-color: #f2f2f2; margin: 0px; padding: 0px;"><span style="margin: 0px; color: #ff00ff; padding: 0px;">50</span></span>,<span style="background-color: #f2f2f2; margin: 0px; padding: 0px;"><span style="margin: 0px; color: #ff00ff; padding: 0px;">50</span></span>,<span style="background-color: #f2f2f2; margin: 0px; padding: 0px;"><span style="margin: 0px; color: #ff00ff; padding: 0px;">300</span></span>,<span style="background-color: #f2f2f2; margin: 0px; padding: 0px;"><span style="margin: 0px; color: #ff00ff; padding: 0px;">150</span></span> <span style="margin: 0px; color: #0000ff; padding: 0px;">;设置窗口：大小、位置</span>

<span style="margin: 0px; color: #804040; padding: 0px;">**winset**</span> AlwaysOnTop, ON, vtano.text<span style="margin: 0px; color: #0000ff; padding: 0px;">;设置窗口：置顶</span>

<span style="margin: 0px; color: #804040; padding: 0px;">**WinSet**</span>, TransColor, 00FF00, vtano.text<span style="margin: 0px; color: #0000ff; padding: 0px;">;设置窗口：将绿色部分设为透明</span>

<span style="margin: 0px; font-family: NSimsun; padding: 0px;">脚本完。</span>

<span style="margin: 0px; font-family: NSimsun; padding: 0px;">注：上述内容以Vim的:TOhtml功能导出。</span>

还可以参考：[http://it.chinawin.net/softwaredev/article-2229f.html](http://it.chinawin.net/softwaredev/article-2229f.html)

[http://blog.chinaunix.net/space.php?uid=25183821&amp;do=blog&amp;cuid=2489087](http://blog.chinaunix.net/space.php?uid=25183821&amp;do=blog&amp;cuid=2489087)