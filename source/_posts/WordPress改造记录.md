title: WordPress改造记录
tags:
  - WordPress
  - WordPress，代码高亮，定制样式，自定义css，自定义js
date: 2010-08-11 11:41:31
---

安装了wp，但是很多地方不足以满足需求，记录一下自己的改造部分。

#### 1、代码高亮

做这行工作怎能没有代码，高亮就是第一步想要实现的。

使用Google SyntaxHighlighter高亮插件，直接在添加插件页面搜索即可，然后安装。有Usage页面链接到http://code.google.com/p/syntaxhighlighter/wiki/Usage，看了一下，两种定义方式，使用pre或textarea标签：

> <pre>&lt;pre name="code" class="c-sharp"&gt;> 
> ... some code here ...> 
> &lt;/pre&gt;> 
> Or> 
> &lt;textarea name="code" class="c#" cols="60" rows="10"&gt;> 
> ... some code here ...> 
> &lt;/textarea&gt;> 
> </pre>

另外希望编辑器上有个按钮，点击插入源码功能。这个自然应该是涉及到TinyMCE工具栏定制，遂搜索了一下有关代码高亮的内容（本以为这个插件安装后自动有这个功能。。原来是要手动编辑html）。这时发现了一篇说“[Google Syntax Highlighter 与WordPress – tinyMCE 的兼容性](http://www.gosoa.com.cn/google-syntax-highlighter-与wordpress-tinymce-的兼容性/)”， 原来pre标签插入是有问题的，pre的name属性在编辑器的“可视化”与“HTML”方式切换的时候，会将name属性去掉，因为xhtml里name不是pre的标准属性。于是测试了一下textarea标签，发现转换以后，源码的换行会被转为&lt;br /&gt;显示在文本域中。。。不尽人意啊。于是按那篇博客所说的方法改造了一下。

> <pre>在 wp-admin/includes/post.php 里面，搜素到$initArray变量的赋值行，在最后添加一个值：> 
> 
> 'extended_valid_elements' =&gt; "pre[name|class]"> 
> 注：如果你是新手或不懂代码，没看懂以上简略的引用，就去看引用页面吧（上面加了链接的），比较详细。> 
> </pre>

这个意思看着就明白，额外的有效元素pre拥有有效的属性name和class。这样在编辑器两种编辑模式下转换，就不会丢失name属性了。

编辑器代码插入按钮还在研究ing&#8230;

2010-8-16注：今天找到了一个新插件：[Auto SyntaxHighlighter](http://wordpress.org/extend/plugins/auto-syntaxhighlighter/)，安装以后编辑器就可以拥有插入代码功能了，试了一下，浏览器查看源码发现跟Google SyntaxHighlighter高亮插件有重复的文件引用，所以保留一个插件就可以了。可以在安装新插件的页面搜索：Auto SyntaxHighlighter即可。截图：

![](http://s.wordpress.org/extend/plugins/auto-syntaxhighlighter/screenshot-2.png?r=276351 "auto-syntaxhighlighter")

<span id="more-35"></span>

#### 2、改造编辑器内容样式。稍微好看点。

找到/wp-includes/js/tinymce/themes/advanced/skins/wp_theme/content.css，并添加或修改以下内容：

<pre name="code" class="css">/* new */
p {margin:5px 0;text-indent:2em;}
blockquote{padding:10px;border-left:3px solid #6c6;background:#f8f8f8;}
/* modify */
pre {padding:0;}
body {word-wrap:break-word;word-break:break-all;}
</pre>

#### 3、利用wp的自定义域，给博文页面添加自定义css，自定义js，引用js文件

参考：http://codex.wordpress.org/Function_Reference/get_post_meta

约定：自定义css名称 ： css， 自定义js名称： js， 引用的js源文件地址： js-src

在当前所用主题的目录，找到header.php和footer.php两个文件，编辑。

header.php 在&lt;head&gt;标签内添加如下代码，引入自定义css：

<pre name="code" class="php">&lt;?php
$css = get_post_meta($post-&gt;ID, 'css', true);
if (!empty($css)) :
?&gt;
&lt;style type="text/css"&gt;
&lt;?php echo $css ?&gt;
&lt;/style&gt;
&lt;?php endif;?&gt;
</pre>

footer.php在&lt;/body&gt;闭合标签前面加入以下代码：

<pre name="code" class="php">&lt;?php
$jssrc = get_post_meta($post-&gt;ID, 'js-src');
$js = get_post_meta($post-&gt;ID, 'js', true);

if (count($jssrc) != 0) {
foreach ($jssrc as $src) {
echo "&lt;script type='text/javascript' src='" . $src . "' &gt;&lt;/script&gt;";
};
}
?&gt;
&lt;?php if(!empty($js)) : ?&gt;

&lt;script type="text/javascript"&gt;
// &lt;![CDATA[
&lt;?php echo $js ?&gt;
// ]]&gt;
&lt;/script&gt;
&lt;?php endif;?&gt;
</pre>

<del datetime="2010-08-11T14:36:31+00:00">以上代码丑了些</del>，pre插入代码高亮在html和可视化编辑之间切换 会过滤php语法，看来得另外折腾了。另外本人不会php，if语法是照搬WordPress文档示例，foreach乃Google而来。凑合用吧，有个小插曲，在写if()之后没写:冒号，导致上传覆盖原文件以后浏览文章是空白了，多谢群里朋友[喜羊羊](http://www.usephp.cn/)提示。之后测试了一下，效果都有了。

测试：发布一个新文章，在自定义域那里添加4个元数据，分别为:  css, js, js-src, js-src ，内容就不说了，懂的自懂，不懂的貌似不会需要这个功能吧。