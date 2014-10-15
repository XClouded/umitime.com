title: 开发者的SEO – 标签的设置
tags:
  - solution
  - 拓展
  - link
  - rel
  - seo
date: 2012-02-15 14:21:36
---

从wordpress的页面源码可以看到应用了不少了link标签，除了熟悉的css引入，还有其他一些用法。

知其意义，便可晓其用法，从http://www.w3school.com.cn/tags/att_link_rel.asp摘录如下：

<div>

## 可选的属性

DTD 指示此属性允许在哪种 DTD 中使用。S=Strict, T=Transitional, F=Frameset.

<span id="more-582"></span>

<table class="dataintable">
<tbody>
<tr>
<th>属性</th>
<th>值</th>
<th>描述</th>
<th>DTD</th>
</tr>
<tr>
<td>charset</td>
<td>_charset_</td>
<td>定义被链接文档的字符编码方式。</td>
<td>STF</td>
</tr>
<tr>
<td>href</td>
<td>_URL_</td>
<td>定义被链接文档的位置。</td>
<td>STF</td>
</tr>
<tr>
<td>hreflang</td>
<td>_language_code_</td>
<td>定义被链接文档中文本的语言。</td>
<td>STF</td>
</tr>
<tr>
<td>media</td>
<td>

*   screen
*   tty
*   tv
*   projection
*   handheld
*   print
*   braille
*   aural
*   all
</td>
<td>规定被链接文档将显示在什么设备上。</td>
<td>STF</td>
</tr>
<tr>
<td>rel</td>
<td>

*   alternate
*   appendix
*   bookmark
*   chapter
*   contents
*   copyright
*   glossary
*   help
*   home
*   index
*   next
*   prev
*   section
*   start
*   stylesheet
*   subsection
</td>
<td>定义当前文档与被链接文档之间的关系。</td>
<td>STF</td>
</tr>
<tr>
<td>rev</td>
<td>

*   alternate
*   appendix
*   bookmark
*   chapter
*   contents
*   copyright
*   glossary
*   help
*   home
*   index
*   next
*   prev
*   section
*   start
*   stylesheet
*   subsection
</td>
<td>定义被链接文档与当前文档之间的关系。</td>
<td>STF</td>
</tr>
<tr>
<td>target</td>
<td>

*   _blank
*   _parent
*   _self
*   _top
*   _framename_
</td>
<td>定义在何处加载被链接文档。</td>
<td>TF</td>
</tr>
<tr>
<td>type</td>
<td>_MIME_type_</td>
<td>规定被链接文档的 MIME 类型。</td>
<td>STF</td>
</tr>
</tbody>
</table>
</div>

个人比较关注rel属性，rev刚好与之相反的意义而已。rel的可用值如下：

<table class="dataintable">
<tbody>
<tr>
<th>值</th>
<th>描述</th>
</tr>
<tr>
<td>alternate</td>
<td>文档的替代版本（比如打印页、翻译或镜像）。</td>
</tr>
<tr>
<td>stylesheet</td>
<td>文档的外部样式表。</td>
</tr>
<tr>
<td>start</td>
<td>集合中的第一个文档。</td>
</tr>
<tr>
<td>next</td>
<td>集合中的下一个文档。</td>
</tr>
<tr>
<td>prev</td>
<td>集合中的上一个文档。</td>
</tr>
<tr>
<td>contents</td>
<td>文档的目录。</td>
</tr>
<tr>
<td>index</td>
<td>文档的索引。</td>
</tr>
<tr>
<td>glossary</td>
<td>在文档中使用的词汇的术语表（解释）。</td>
</tr>
<tr>
<td>copyright</td>
<td>包含版权信息的文档。</td>
</tr>
<tr>
<td>chapter</td>
<td>文档的章。</td>
</tr>
<tr>
<td>section</td>
<td>文档的节。</td>
</tr>
<tr>
<td>subsection</td>
<td>文档的小节。</td>
</tr>
<tr>
<td>appendix</td>
<td>文档的附录。</td>
</tr>
<tr>
<td>help</td>
<td>帮助文档。</td>
</tr>
<tr>
<td>bookmark</td>
<td>相关文档。</td>
</tr>
</tbody>
</table>

wordpress常用start, next, prev, index。个人觉得contents, bookmark也是对SEO有帮助的。

Google额外添加支持一个rel值为canonical。其作用是 指定您的URL标准范式。像wordpress开启了固定链接以后，其实有两个链接指向同一篇文章的，比如本文

http://www.cssor.com/developer-seo-link-tag-setting.html和http://www.cssor.com/?p=582都可以访问到，那么不想后者在搜索结果中出现，只出现前者链接，那么就可以在头部添加

&lt;link rel=&#8221;canonical&#8221; href=&#8221;http://www.cssor.com/developer-seo-link-tag-setting.html&#8221;/&gt;

详细关于canonical可以阅读： http://www.google.com.hk/ggblog/googlewebmaster-cn/2009/02/url.html