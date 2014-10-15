title: git subtree用法
tags:
  - 拓展
  - git
  - git subtree
date: 2014-07-30 19:07:09
---

来源于：http://aoxuis.me/posts/2013/08/07/git-subtree/

### 使用场景

例如，在项目Game中有一个子目录AI。Game和AI分别是一个独立的git项目，可以分开维护。为了避免直接复制粘贴代码，我们希望Game中的AI子目录与AI的git项目关联，有3层意思：

1.  AI子目录使用AI的git项目来填充，内容保持一致。
2.  当AI的git项目代码有更新，可以拉取更新到Game项目的AI子目录来。
3.  反过来，当Game项目的AI子目录有变更，还可以推送这些变更到AI的git项目。

用git subtree可以轻松满足上面的需求。

### git subtree用法

<span id="more-1183"></span>

针对第一段的3条需求，我分别说明具体的命令。

#### 1\. 第一次添加子目录，建立与git项目的关联

建立关联总共有2条命令。

语法：`git remote add -f &lt;子仓库名&gt; &lt;子仓库地址&gt;`

解释：其中-f意思是在添加远程仓库之后，立即执行fetch。

语法：`git subtree add --prefix=&lt;子目录名&gt; &lt;子仓库名&gt; &lt;分支&gt; --squash`

解释：&#8211;squash意思是把subtree的改动合并成一次commit，这样就不用拉取子项目完整的历史记录。&#8211;prefix之后的=等号也可以用空格。

示例

<pre>$git remote add -f ai https://github.com/aoxu/ai.git  
$git subtree add --prefix=ai ai master --squash</pre>

#### 2\. 从远程仓库更新子目录

更新子目录有2条命令。

语法：`git fetch &lt;远程仓库名&gt; &lt;分支&gt;`

语法：`git subtree pull --prefix=&lt;子目录名&gt; &lt;远程分支&gt; &lt;分支&gt; --squash`

示例

<pre>$git fetch ai master  
$git subtree pull --prefix=ai ai --squash</pre>

#### 3\. 从子目录push到远程仓库（确认你有写权限）

推送子目录的变更有1条命令。

语法：`git subtree push --prefix=&lt;子目录名&gt; &lt;远程分支名&gt; 分支`

示例

<pre>$git subtree push --prefix=ai ai master</pre>

参考资料：

1.  [speackerdeck](https://speakerdeck.com/cloudsben/git-subtree-ti-dai-git-submodule)
2.  [atlassian](http://blogs.atlassian.com/2013/05/alternatives-to-git-submodule-git-subtree/)
3.  [pro git](http://git-scm.com/book/zh/Git-%E5%B7%A5%E5%85%B7-%E5%AD%90%E6%A0%91%E5%90%88%E5%B9%B6)