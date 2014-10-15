title: git subtree更好的管理项目公共资源
tags:
  - solution
  - 服务器
  - git
  - git submodule
  - git subtree
date: 2014-06-26 11:07:28
---

抛弃git submodule, 拥抱git subtree。

来源于：http://aoxuis.me/posts/2013/08/07/git-subtree/

### 使用场景

例如，在项目Game中有一个子目录AI。Game和AI分别是一个独立的git项目，可以分开维护。为了避免直接复制粘贴代码，我们希望Game中的AI子目录与AI的git项目关联，有3层意思：

1.  AI子目录使用AI的git项目来填充，内容保持一致。
2.  当AI的git项目代码有更新，可以拉取更新到Game项目的AI子目录来。
3.  反过来，当Game项目的AI子目录有变更，还可以推送这些变更到AI的git项目。用git subtree可以轻松满足上面的需求。

<span id="more-1119"></span>

### 对比git submodule

如果你没有用过git submodule，你甚至可以不用了解[git submodule是什么](http://git-scm.com/book/zh/Git-%E5%B7%A5%E5%85%B7-%E5%AD%90%E6%A8%A1%E5%9D%97)，submodule的基本介绍也不在本文的说明范围内。虽然它满足了上述差不多的需求，但是复杂难用，以至于需要[这么长一篇教程](http://www.kafeitu.me/git/2012/03/27/git-submodule.html)才能说清楚用法。

如果你的项目正在使用git submodule，你应该知道用submodule有多么麻烦，这里还有[一篇文章专门解释git submodule的缺点](http://codingkilledthecat.wordpress.com/2012/04/28/why-your-company-shouldnt-use-git-submodules/)，可以参考前面教程里的最后一段来删除submodule（是的，连删除步骤都非常麻烦）。

使用git subtree之后，管理、更新都更加方便。

### 什么是git subtree

git subtree是一条git子命令，本质上subtree是一种合并策略，[从git v1.5.2，官方就推荐使用subtree代替submodule](https://www.kernel.org/pub/software/scm/git/docs/howto/using-merge-subtree.html)，所以它并不需要保存.submodule这样的元信息。

### git subtree的前提条件

subtree子命令很晚才集成到git中，请确保你的git版本（使用git &#8211;version查看） &gt; v1.8.0.0。有些文章中说v1.7.11就已经集成了，实际上没有，如果直接执行会看到这样的结果：

<figure class="figure-code code">
<div class="highlight">
<pre>$git subtree  
git: 'subtree' is not a git command. See 'git --help'.</pre>
</div>
</figure>

如果你是在OS X下使用git，推荐用[homebrew](http://brew.sh/)来安装新版本

<figure class="figure-code code">
<div class="highlight">
<pre>$brew install git  
$git --version  
git version 1.8.3.4</pre>
</div>
</figure>

### git subtree用法

针对第一段的3条需求，我分别说明具体的命令。

#### 1\. 第一次添加子目录，建立与git项目的关联

建立关联总共有2条命令。

语法：`git remote add -f &lt;子仓库名&gt; &lt;子仓库地址&gt;`

解释：其中-f意思是在添加远程仓库之后，立即执行fetch。

语法：`git subtree add --prefix=&lt;子目录名&gt; &lt;子仓库名&gt; &lt;分支&gt; --squash`

解释：&#8211;squash意思是把subtree的改动合并成一次commit，这样就不用拉取子项目完整的历史记录。&#8211;prefix之后的=等号也可以用空格。

示例

<figure class="figure-code code">
<div class="highlight">
<pre>$git remote add -f ai https://github.com/aoxu/ai.git  
$git subtree add --prefix=ai ai master --squash</pre>
</div>
</figure>

#### 2\. 从远程仓库更新子目录

更新子目录有2条命令。

语法：`git fetch &lt;远程仓库名&gt; &lt;分支&gt;`

语法：`git subtree pull --prefix=&lt;子目录名&gt; &lt;远程分支&gt; &lt;分支&gt; --squash`

示例

<figure class="figure-code code">
<div class="highlight">
<pre>$git fetch ai master  
$git subtree pull --prefix=ai ai --squash</pre>
</div>
</figure>

#### 3\. 从子目录push到远程仓库（确认你有写权限）

推送子目录的变更有1条命令。

语法：`git subtree push --prefix=&lt;子目录名&gt; &lt;远程分支名&gt; 分支`

示例

<figure class="figure-code code">
<div class="highlight">
<pre>$git subtree push --prefix=ai ai master</pre>
</div>
</figure>

参考资料：

1.  [speackerdeck](https://speakerdeck.com/cloudsben/git-subtree-ti-dai-git-submodule)
2.  [atlassian](http://blogs.atlassian.com/2013/05/alternatives-to-git-submodule-git-subtree/)
3.  [pro git](http://git-scm.com/book/zh/Git-%E5%B7%A5%E5%85%B7-%E5%AD%90%E6%A0%91%E5%90%88%E5%B9%B6)