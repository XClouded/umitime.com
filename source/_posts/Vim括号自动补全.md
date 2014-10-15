title: Vim括号自动补全
tags:
  - vim
date: 2011-09-16 09:33:04
---

来源于：

在 Vim 中实现括号自动补全

将下面的代码加入到 ~/.vimrc 中，重启 Vim，即可：

<pre class="brush:shell">:inoremap ( ()&lt;ESC&gt;i
 :inoremap ) &lt;c-r&gt;=ClosePair(')')&lt;CR&gt;
 :inoremap { {}&lt;ESC&gt;i
 :inoremap } &lt;c-r&gt;=ClosePair('}')&lt;CR&gt;
 :inoremap [ []&lt;ESC&gt;i
 :inoremap ] &lt;c-r&gt;=ClosePair(']')&lt;CR&gt;
 :inoremap &lt; &lt;&gt;&lt;ESC&gt;i
 :inoremap &gt; &lt;c-r&gt;=ClosePair('&gt;')&lt;CR&gt;

 function ClosePair(char)
   if getline('.')[col('.') - 1] == a:char
     return "\&lt;Right&gt;"
   else
     return a:char
   endif
 endf</pre>