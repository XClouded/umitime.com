title: Mac下可视化解决git conflict
tags:
  - 拓展
  - diffmerge
  - git mergetool
date: 2014-01-07 19:05:39
---

不比Windows系统软件的丰富和可视化的强大，虽然Mac也还好，个人感觉还是差些。这不，git一旦发生冲突，想找个好些的可视化解决冲突的软件都有些不容易。后来搜到DiffMerge这个软件，于是尝试了下。命令行调用为： git mergetool ，但是需要设置一下：

首先下载时要下载installer格式：[Download the DiffMerge OS X installer](http://www.sourcegear.com/diffmerge/downloads.php "DiffMerge Installer for Mac OS X")

其次在命令行执行配置：

<pre>git config --global diff.tool diffmerge
git config --global difftool.diffmerge.cmd 'diffmerge "$LOCAL" "$REMOTE"'
git config --global merge.tool diffmerge
git config --global mergetool.diffmerge.cmd 'diffmerge --merge --result="$MERGED" "$LOCAL" "$(if test -f "$BASE"; then echo "$BASE"; else echo "$LOCAL"; fi)" "$REMOTE"'
git config --global mergetool.diffmerge.trustExitCode true</pre>

以上内容来自http://quding0308.iteye.com/blog/1969831