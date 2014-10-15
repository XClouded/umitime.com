title: 使用hexo基于github pages做博客镜像
tags:
  - 自然
  - hexo
  - hexo wordpress
date: 2014-10-14 12:10:27
---

基于GitHub Pages为cssor.com做了一个[镜像](http://tofishes.github.io)，采用的引擎是hexo。

过程中出现一些问题，逐一解决一下。

1、迁移到hexo，使用wordpress的RSS做数据源

我的rss默认只推送最近10条，需要在WP仪表盘下的 设置 → 阅读 项目下更改Feed中显示数目，改成比文章数多就行了。

2、某些迁移后的Markdown文件在generate时出现语法报错，例如：

<pre>
SyntaxError: Unexpected token ; # 或如下面
Error: Unexpected colon on line 1588 in file
</pre>

不好改，遂暂时将个别报错文件删除了事。

3、文件数过多，达到IO限制报错，如Error: EMFILE, open &#8220;some file path&#8230;&#8221;，解决如下：

<pre>
ulimit -n 5000 #改为一个较大的数
</pre>

这里有个参考： http://bubbyroom.com/2013/11/08/hexo-deploy-err/

基本目前遇到的问题就这些。