title: Nginx Rewrite的规则
tags:
  - 服务器
  - nginx
  - nginx rewrite
date: 2011-12-14 10:29:57
---

来源，未验证，参考即可：[http://blog.cafeneko.info/2010/10/nginx_rewrite_note/](http://blog.cafeneko.info/2010/10/nginx_rewrite_note/)

在新主机的迁移过程中,最大的困难就是WP permalink rewrite的设置.

因为旧主机是用的Apache, 使用的是WP本身就可以更改的.htaccess,没有太大的难度.而这次在VPS上跑的是Nginx,主要是因为Nginx的速度比Apache要快很多.

但是另一方面就不是那么舒服了,因为Nginx的rewrite跟Apache不同,而且是在服务器上面才能更改.

下面是其间的一些研究笔记.(以下用例如无特别说明均摘自nginx wiki)

<span id="more-559"></span>

## /1 Nginx rewrite基本语法

&nbsp;

Nginx的rewrite语法其实很简单.用到的指令无非是这几个

*   set
*   if
*   return
*   break
*   rewrite

麻雀虽小,<span style="text-decoration: line-through;">可御可萝</span>五脏俱全.只是简单的几个指令却可以做出绝对不输apache的简单灵活的配置.

1.set

set主要是用来设置变量用的,没什么特别的

2.if

if主要用来判断一些在rewrite语句中无法直接匹配的条件,比如检测文件存在与否,http header,cookie等,

> 用法: if(条件) {…}

- 当if表达式中的条件为true,则执行if块中的语句

- 当表达式只是一个变量时,如果值为空或者任何以0开头的字符串都会当作false

- 直接比较内容时,使用 = 和 !=

- 使用正则表达式匹配时,使用

> ~ 大小写敏感匹配
> 
> ~* 大小写不敏感匹配
> 
> !~ 大小写敏感不匹配
> 
> !~* 大小写不敏感不匹配

这几句话看起来有点绕,总之记住: ~为正则匹配, 后置*为大小写不敏感, 前置!为”非”操作

随便一提,因为nginx使用花括号{}判断区块,所以当正则中包含花括号时,则必须用双引号将正则包起来.对下面讲到的rewrite语句中的正则亦是如此.

比如 “\d{4}\d{2}\.+”

- 使用-f,-d,-e,-x检测文件和目录

> -f 检测文件存在
> 
> -d 检测目录存在
> 
> -e 检测文件,目录或者符号链接存在
> 
> -x 检测文件可执行

跟~类似,前置!则为”非”操作

举例

<div class="wp_syntax">
<div class="code">
<pre class="bash" style="font-family: monospace;"><span style="color: #000000; font-weight: bold;">if</span> <span style="color: #7a0874; font-weight: bold;">(</span><span style="color: #007800;">$http_user_agent</span> ~ MSIE<span style="color: #7a0874; font-weight: bold;">)</span> <span style="color: #7a0874; font-weight: bold;">{</span>
  rewrite  ^<span style="color: #7a0874; font-weight: bold;">(</span>.<span style="color: #000000; font-weight: bold;">*</span><span style="color: #7a0874; font-weight: bold;">)</span>$  <span style="color: #000000; font-weight: bold;">/</span>msie<span style="color: #000000; font-weight: bold;">/</span><span style="color: #007800;">$1</span>  <span style="color: #7a0874; font-weight: bold;">break</span>;
<span style="color: #7a0874; font-weight: bold;">}</span></pre>
</div>
</div>

//如果UA包含”MSIE”,rewrite 请求到/msie目录下

<div class="wp_syntax">
<div class="code">
<pre class="bash" style="font-family: monospace;"><span style="color: #000000; font-weight: bold;">if</span> <span style="color: #7a0874; font-weight: bold;">(</span><span style="color: #007800;">$http_cookie</span> ~<span style="color: #000000; font-weight: bold;">*</span> <span style="color: #ff0000;">"id=([^;] +)(?:;|$)"</span> <span style="color: #7a0874; font-weight: bold;">)</span> <span style="color: #7a0874; font-weight: bold;">{</span>
  <span style="color: #000000; font-weight: bold;">set</span>  <span style="color: #007800;">$id</span>  <span style="color: #007800;">$1</span>;
<span style="color: #7a0874; font-weight: bold;">}</span></pre>
</div>
</div>

//如果cookie匹配正则,设置变量$id等于正则引用部分

<div class="wp_syntax">
<div class="code">
<pre class="bash" style="font-family: monospace;"><span style="color: #000000; font-weight: bold;">if</span> <span style="color: #7a0874; font-weight: bold;">(</span><span style="color: #007800;">$request_method</span> = POST <span style="color: #7a0874; font-weight: bold;">)</span> <span style="color: #7a0874; font-weight: bold;">{</span>
  <span style="color: #7a0874; font-weight: bold;">return</span> <span style="color: #000000;">405</span>;
<span style="color: #7a0874; font-weight: bold;">}</span></pre>
</div>
</div>

//如果提交方法为POST,则返回状态405 (Method not allowed)

<div class="wp_syntax">
<div class="code">
<pre class="bash" style="font-family: monospace;"><span style="color: #000000; font-weight: bold;">if</span> <span style="color: #7a0874; font-weight: bold;">(</span><span style="color: #000000; font-weight: bold;">!</span>-f <span style="color: #007800;">$request_filename</span><span style="color: #7a0874; font-weight: bold;">)</span> <span style="color: #7a0874; font-weight: bold;">{</span>
  <span style="color: #7a0874; font-weight: bold;">break</span>;
  proxy_pass  http:<span style="color: #000000; font-weight: bold;">//</span>127.0.0.1;
<span style="color: #7a0874; font-weight: bold;">}</span></pre>
</div>
</div>

//如果请求文件名不存在,则反向代理localhost

<div class="wp_syntax">
<div class="code">
<pre class="bash" style="font-family: monospace;"><span style="color: #000000; font-weight: bold;">if</span> <span style="color: #7a0874; font-weight: bold;">(</span><span style="color: #007800;">$args</span> ~ <span style="color: #007800;">post</span>=<span style="color: #000000;">140</span><span style="color: #7a0874; font-weight: bold;">)</span><span style="color: #7a0874; font-weight: bold;">{</span>
  rewrite ^ http:<span style="color: #000000; font-weight: bold;">//</span>example.com<span style="color: #000000; font-weight: bold;">/</span> permanent;
<span style="color: #7a0874; font-weight: bold;">}</span></pre>
</div>
</div>

//如果query string中包含”post=140″,永久重定向到example.com

3.return

return可用来直接设置HTTP返回状态,比如403,404等(301,302不可用return返回,这个下面会在rewrite提到)

4.break

立即停止rewrite检测,跟下面讲到的rewrite的break flag功能是一样的,区别在于前者是一个语句,后者是rewrite语句的flag

5.rewrite

最核心的功能(废话)

> 用法: rewrite 正则 替换 标志位

其中标志位有四种

> break – 停止rewrite检测,也就是说当含有break flag的rewrite语句被执行时,该语句就是rewrite的最终结果
> 
> last – 停止rewrite检测,但是跟break有本质的不同,last的语句<span style="font-weight: bold; color: #ff0000;">不一定</span>是最终结果,这点后面会跟nginx的location匹配一起提到
> 
> redirect – 返回302临时重定向,一般用于重定向到完整的URL(包含http:部分)
> 
> permanent – 返回301永久重定向,一般用于重定向到完整的URL(包含http:部分)

因为301和302不能简单的只单纯返回状态码,还必须有重定向的URL,这就是return指令无法返回301,302的原因了. 作为替换,rewrite可以更灵活的使用redirect和permanent标志实现301和302\. 比如上一篇日志中提到的Blog搬家要做的域名重定向,在nginx中就会这么写

<div class="wp_syntax">
<div class="code">
<pre class="bash" style="font-family: monospace;">rewrite ^<span style="color: #7a0874; font-weight: bold;">(</span>.<span style="color: #000000; font-weight: bold;">*</span><span style="color: #7a0874; font-weight: bold;">)</span>$ http:<span style="color: #000000; font-weight: bold;">//</span>newdomain.com<span style="color: #000000; font-weight: bold;">/</span> permanent;</pre>
</div>
</div>

举例来说一下rewrite的实际应用

<div class="wp_syntax">
<div class="code">
<pre class="bash" style="font-family: monospace;">rewrite  ^<span style="color: #7a0874; font-weight: bold;">(</span><span style="color: #000000; font-weight: bold;">/</span>download<span style="color: #000000; font-weight: bold;">/</span>.<span style="color: #000000; font-weight: bold;">*</span><span style="color: #7a0874; font-weight: bold;">)</span><span style="color: #000000; font-weight: bold;">/</span>media<span style="color: #000000; font-weight: bold;">/</span><span style="color: #7a0874; font-weight: bold;">(</span>.<span style="color: #000000; font-weight: bold;">*</span><span style="color: #7a0874; font-weight: bold;">)</span>\..<span style="color: #000000; font-weight: bold;">*</span>$  <span style="color: #007800;">$1</span><span style="color: #000000; font-weight: bold;">/</span>mp3<span style="color: #000000; font-weight: bold;">/</span><span style="color: #007800;">$2</span>.mp3  <span style="color: #c20cb9; font-weight: bold;">last</span>;</pre>
</div>
</div>

如果请求为 /download/eva/media/op1.mp3 则请求被rewrite到 /download/eva/mp3/op1.mp3

使用起来就是这样,很简单不是么? 不过要注意的是rewrite有很多潜规则需要注意

- rewrite的生效区块为sever, location, if

- rewrite只对相对路径进行匹配,不包含hostname 比如说以上面301重定向的例子说明

<div class="wp_syntax">
<div class="code">
<pre class="bash" style="font-family: monospace;">rewrite ~<span style="color: #000000; font-weight: bold;">*</span> cafeneko\.info http:<span style="color: #000000; font-weight: bold;">//</span>newdomain.com<span style="color: #000000; font-weight: bold;">/</span> permanent;</pre>
</div>
</div>

这句是永远无法执行的,以这个URL为例

> http://blog.cafeneko.info/2010/10/neokoseseiki_in_new_home/?utm_source=rss&amp;utm_medium=rss&amp;utm_campaign=neokoseseiki_in_new_home

其中cafeneko.info叫做hostname,再往后到?为止叫做相对路径,?后面的一串叫做query string

对于rewrite来说,其正则表达式仅对”/2010/10/neokoseseiki_in_new_home”这一部分进行匹配,即不包含hostname,也不包含query string .所以除非相对路径中包含跟域名一样的string,否则是不会匹配的. 如果非要做域名匹配的话就要使用if语句了,比如进行去www跳转

<div class="wp_syntax">
<div class="code">
<pre class="bash" style="font-family: monospace;"><span style="color: #000000; font-weight: bold;">if</span> <span style="color: #7a0874; font-weight: bold;">(</span><span style="color: #007800;">$host</span> ~<span style="color: #000000; font-weight: bold;">*</span> ^www\.<span style="color: #7a0874; font-weight: bold;">(</span>cafeneko\.info<span style="color: #7a0874; font-weight: bold;">)</span><span style="color: #7a0874; font-weight: bold;">)</span> <span style="color: #7a0874; font-weight: bold;">{</span>
  <span style="color: #000000; font-weight: bold;">set</span> <span style="color: #007800;">$host_without_www</span> <span style="color: #007800;">$1</span>;
  rewrite ^<span style="color: #7a0874; font-weight: bold;">(</span>.<span style="color: #000000; font-weight: bold;">*</span><span style="color: #7a0874; font-weight: bold;">)</span>$ http:<span style="color: #000000; font-weight: bold;">//</span><span style="color: #007800;">$host_without_www</span><span style="color: #007800;">$1</span> permanent;
<span style="color: #7a0874; font-weight: bold;">}</span></pre>
</div>
</div>

- 使用相对路径rewrite时,会根据HTTP header中的HOST跟nginx的server_name匹配后进行rewrite,如果HOST不匹配或者没有HOST信息的话则rewrite到server_name设置的第一个域名,如果没有设置server_name的话,会使用本机的localhost进行rewrite

- 前面提到过,rewrite的正则是不匹配query string的,所以默认情况下,query string是自动追加到rewrite后的地址上的,如果不想自动追加query string,则在rewrite地址的末尾添加?

<div class="wp_syntax">
<div class="code">
<pre class="bash" style="font-family: monospace;">rewrite  ^<span style="color: #000000; font-weight: bold;">/</span>users<span style="color: #000000; font-weight: bold;">/</span><span style="color: #7a0874; font-weight: bold;">(</span>.<span style="color: #000000; font-weight: bold;">*</span><span style="color: #7a0874; font-weight: bold;">)</span>$  <span style="color: #000000; font-weight: bold;">/</span>show?<span style="color: #007800;">user</span>=<span style="color: #007800;">$1</span>?  <span style="color: #c20cb9; font-weight: bold;">last</span>;</pre>
</div>
</div>

rewrite的基本知识就是这么多..但还没有完..还有最头疼的部分没有说…

&nbsp;

## /2 Nginx location 和 rewrite retry

&nbsp;

nginx的rewrite有个很奇特的特性 — rewrite后的url会再次进行rewrite检查,最多重试10次,10次后还没有终止的话就会返回HTTP 500

用过nginx的朋友都知道location区块,location区块有点像Apache中的RewriteBase,但对于nginx来说location是控制的级别而已,里面的内容不仅仅是rewrite.

这里必须稍微先讲一点location的知识.location是nginx用来处理对同一个server不同的请求地址使用独立的配置的方式

举例:

<div class="wp_syntax">
<div class="code">
<pre class="bash" style="font-family: monospace;">location  = <span style="color: #000000; font-weight: bold;">/</span> <span style="color: #7a0874; font-weight: bold;">{</span>
  ....配置A
<span style="color: #7a0874; font-weight: bold;">}</span>

location  <span style="color: #000000; font-weight: bold;">/</span> <span style="color: #7a0874; font-weight: bold;">{</span>
  ....配置B
<span style="color: #7a0874; font-weight: bold;">}</span>

location ^~ <span style="color: #000000; font-weight: bold;">/</span>images<span style="color: #000000; font-weight: bold;">/</span> <span style="color: #7a0874; font-weight: bold;">{</span>
  ....配置C
<span style="color: #7a0874; font-weight: bold;">}</span>

location ~<span style="color: #000000; font-weight: bold;">*</span> \.<span style="color: #7a0874; font-weight: bold;">(</span>gif<span style="color: #000000; font-weight: bold;">|</span>jpg<span style="color: #000000; font-weight: bold;">|</span>jpeg<span style="color: #7a0874; font-weight: bold;">)</span>$ <span style="color: #7a0874; font-weight: bold;">{</span>
  ....配置D
<span style="color: #7a0874; font-weight: bold;">}</span></pre>
</div>
</div>

访问 / 会使用配置A

访问 /documents/document.html 会使用配置B

访问 /images/1.gif 会使用配置C

访问 /documents/1.jpg 会使用配置D

如何判断命中哪个location暂且按下不婊, 我们在实战篇再回头来看这个问题.

现在我们只需要明白一个情况: nginx可以有多个location并使用不同的配.

sever区块中如果有包含rewrite规则,则会最先执行,而且只会执行一次, 然后再判断命中哪个location的配置,再去执行该location中的rewrite, 当该location中的rewrite执行完毕时,rewrite并不会停止,而是根据rewrite过的URL再次判断location并执行其中的配置. 那么,这里就存在一个问题,如果rewrite写的不正确的话,是会在location区块间造成无限循环的.所以nginx才会加一个最多重试10次的上限. 比如这个例子

<div class="wp_syntax">
<div class="code">
<pre class="bash" style="font-family: monospace;">location <span style="color: #000000; font-weight: bold;">/</span>download<span style="color: #000000; font-weight: bold;">/</span> <span style="color: #7a0874; font-weight: bold;">{</span>
  rewrite  ^<span style="color: #7a0874; font-weight: bold;">(</span><span style="color: #000000; font-weight: bold;">/</span>download<span style="color: #000000; font-weight: bold;">/</span>.<span style="color: #000000; font-weight: bold;">*</span><span style="color: #7a0874; font-weight: bold;">)</span><span style="color: #000000; font-weight: bold;">/</span>media<span style="color: #000000; font-weight: bold;">/</span><span style="color: #7a0874; font-weight: bold;">(</span>.<span style="color: #000000; font-weight: bold;">*</span><span style="color: #7a0874; font-weight: bold;">)</span>\..<span style="color: #000000; font-weight: bold;">*</span>$  <span style="color: #007800;">$1</span><span style="color: #000000; font-weight: bold;">/</span>mp3<span style="color: #000000; font-weight: bold;">/</span><span style="color: #007800;">$2</span>.mp3  <span style="color: #c20cb9; font-weight: bold;">last</span>;
<span style="color: #7a0874; font-weight: bold;">}</span></pre>
</div>
</div>

如果请求为 /download/eva/media/op1.mp3 则请求被rewrite到 /download/eva/mp3/op1.mp3

结果rewrite的结果重新命中了location /download/ 虽然这次并没有命中rewrite规则的正则表达式,但因为缺少终止rewrite的标志,其仍会不停重试download中rewrite规则直到达到10次上限返回HTTP 500

认真的朋友这时就会问了,上面的rewrite规则不是有标志位last么? last不是终止rewrite的意思么?

说到这里我就要抱怨下了,网上能找到关于nginx rewrite的文章中80%对last标志的解释都是

> last – 基本上都用这个Flag

……这他妈坑爹呢!!! 什么叫基本上都用? 什么是不基本的情况?  =皿=

有兴趣的可以放狗”基本上都用这个Flag”…

我最终还是在stack overflow找到了答案:

last和break最大的不同在于

- break是终止当前location的rewrite检测,而且不再进行location匹配

– last是终止当前location的rewrite检测,但会继续重试location匹配并处理区块中的rewrite规则

还是这个该死的例子

<div class="wp_syntax">
<div class="code">
<pre class="bash" style="font-family: monospace;">location <span style="color: #000000; font-weight: bold;">/</span>download<span style="color: #000000; font-weight: bold;">/</span> <span style="color: #7a0874; font-weight: bold;">{</span>
  rewrite  ^<span style="color: #7a0874; font-weight: bold;">(</span><span style="color: #000000; font-weight: bold;">/</span>download<span style="color: #000000; font-weight: bold;">/</span>.<span style="color: #000000; font-weight: bold;">*</span><span style="color: #7a0874; font-weight: bold;">)</span><span style="color: #000000; font-weight: bold;">/</span>media<span style="color: #000000; font-weight: bold;">/</span><span style="color: #7a0874; font-weight: bold;">(</span>.<span style="color: #000000; font-weight: bold;">*</span><span style="color: #7a0874; font-weight: bold;">)</span>\..<span style="color: #000000; font-weight: bold;">*</span>$  <span style="color: #007800;">$1</span><span style="color: #000000; font-weight: bold;">/</span>mp3<span style="color: #000000; font-weight: bold;">/</span><span style="color: #007800;">$2</span>.mp3  ;
  rewrite  ^<span style="color: #7a0874; font-weight: bold;">(</span><span style="color: #000000; font-weight: bold;">/</span>download<span style="color: #000000; font-weight: bold;">/</span>.<span style="color: #000000; font-weight: bold;">*</span><span style="color: #7a0874; font-weight: bold;">)</span><span style="color: #000000; font-weight: bold;">/</span>movie<span style="color: #000000; font-weight: bold;">/</span><span style="color: #7a0874; font-weight: bold;">(</span>.<span style="color: #000000; font-weight: bold;">*</span><span style="color: #7a0874; font-weight: bold;">)</span>\..<span style="color: #000000; font-weight: bold;">*</span>$  <span style="color: #007800;">$1</span><span style="color: #000000; font-weight: bold;">/</span>avi<span style="color: #000000; font-weight: bold;">/</span><span style="color: #007800;">$2</span>.mp3  ;
  rewrite  ^<span style="color: #7a0874; font-weight: bold;">(</span><span style="color: #000000; font-weight: bold;">/</span>download<span style="color: #000000; font-weight: bold;">/</span>.<span style="color: #000000; font-weight: bold;">*</span><span style="color: #7a0874; font-weight: bold;">)</span><span style="color: #000000; font-weight: bold;">/</span>avvvv<span style="color: #000000; font-weight: bold;">/</span><span style="color: #7a0874; font-weight: bold;">(</span>.<span style="color: #000000; font-weight: bold;">*</span><span style="color: #7a0874; font-weight: bold;">)</span>\..<span style="color: #000000; font-weight: bold;">*</span>$  <span style="color: #007800;">$1</span><span style="color: #000000; font-weight: bold;">/</span>rmvb<span style="color: #000000; font-weight: bold;">/</span><span style="color: #007800;">$2</span>.mp3 ;
<span style="color: #7a0874; font-weight: bold;">}</span></pre>
</div>
</div>

上面没有写标志位,请各位自行脑补…

如果请求为 /download/acg/moive/UBW.avi

last的情况是: 在第2行rewrite处终止,并重试location /download..死循环

break的情况是: 在第2行rewrite处终止,其结果为最终的rewrite地址.

也就是说,上面的某位试图下载eva op不但没下到反而被HTTP 500射了一脸的例子正是因为用了last标志所以才会造成死循环,如果用break就没事了.

<div class="wp_syntax">
<div class="code">
<pre class="bash" style="font-family: monospace;">location <span style="color: #000000; font-weight: bold;">/</span>download<span style="color: #000000; font-weight: bold;">/</span> <span style="color: #7a0874; font-weight: bold;">{</span>
  rewrite  ^<span style="color: #7a0874; font-weight: bold;">(</span><span style="color: #000000; font-weight: bold;">/</span>download<span style="color: #000000; font-weight: bold;">/</span>.<span style="color: #000000; font-weight: bold;">*</span><span style="color: #7a0874; font-weight: bold;">)</span><span style="color: #000000; font-weight: bold;">/</span>media<span style="color: #000000; font-weight: bold;">/</span><span style="color: #7a0874; font-weight: bold;">(</span>.<span style="color: #000000; font-weight: bold;">*</span><span style="color: #7a0874; font-weight: bold;">)</span>\..<span style="color: #000000; font-weight: bold;">*</span>$  <span style="color: #007800;">$1</span><span style="color: #000000; font-weight: bold;">/</span>mp3<span style="color: #000000; font-weight: bold;">/</span><span style="color: #007800;">$2</span>.mp3  <span style="color: #7a0874; font-weight: bold;">break</span>;
<span style="color: #7a0874; font-weight: bold;">}</span></pre>
</div>
</div>

对于这个问题,我个人的建议是,如果是全局性质的rewrite,最好放在server区块中并减少不必要的location区块.location区块中的rewrite要想清楚是用last还是break.

有人可能会问,用break不就万无一失了么?

不对.有些情况是要用last的. 典型的例子就是wordpress的permalink rewrite

常见的情况下, wordpress的rewrite是放在location /下面,并将请求rewrite到/index.php

这时如果这里使用break乃就挂了,不信试试. ｂ（￣▽￣）ｄ…因为nginx返回的是没有解释的index.php的源码…

这里一定要使用last才可以在结束location / 的rewrite, 并再次命中location ~ \.php$,将其交给fastcgi进行解释.其后返回给浏览器的才是解释过的html代码.

关于nginx rewrite的简介到这里就全部讲完了,水平及其有限,请大家指出错漏…

&nbsp;

## /3 实战! WordPress的Permalink+Supercache rewrite实现

&nbsp;

这个rewrite写法其实是来自supercache作者本家的某个评论中,网上很容易查到,做了一些修改. 先给出该配置文件的全部内容..部分内容码掉了..绝对路径什么的你知道也没啥用对吧?

<div class="wp_syntax">
<div class="code">
<pre class="bash" style="font-family: monospace;">server <span style="color: #7a0874; font-weight: bold;">{</span>
	listen   <span style="color: #000000;">80</span>;
	server_name  cafeneko.info www.cafeneko.info;

	access_log  <span style="color: #000000; font-weight: bold;">***</span>;
	error_log   <span style="color: #000000; font-weight: bold;">***</span> ;

	root   <span style="color: #000000; font-weight: bold;">***</span>;
	index  index.php;

	gzip_static on;

	<span style="color: #000000; font-weight: bold;">if</span> <span style="color: #7a0874; font-weight: bold;">(</span>-f <span style="color: #007800;">$request_filename</span><span style="color: #7a0874; font-weight: bold;">)</span> <span style="color: #7a0874; font-weight: bold;">{</span>
		<span style="color: #7a0874; font-weight: bold;">break</span>;
	<span style="color: #7a0874; font-weight: bold;">}</span>

	<span style="color: #000000; font-weight: bold;">set</span> <span style="color: #007800;">$supercache_file</span> <span style="color: #ff0000;">''</span>;
	<span style="color: #000000; font-weight: bold;">set</span> <span style="color: #007800;">$supercache_uri</span> <span style="color: #007800;">$request_uri</span>;

	<span style="color: #000000; font-weight: bold;">if</span> <span style="color: #7a0874; font-weight: bold;">(</span><span style="color: #007800;">$request_method</span> = POST<span style="color: #7a0874; font-weight: bold;">)</span> <span style="color: #7a0874; font-weight: bold;">{</span>
		<span style="color: #000000; font-weight: bold;">set</span> <span style="color: #007800;">$supercache_uri</span> <span style="color: #ff0000;">''</span>;
	<span style="color: #7a0874; font-weight: bold;">}</span>

	<span style="color: #000000; font-weight: bold;">if</span> <span style="color: #7a0874; font-weight: bold;">(</span><span style="color: #007800;">$query_string</span><span style="color: #7a0874; font-weight: bold;">)</span> <span style="color: #7a0874; font-weight: bold;">{</span>
		<span style="color: #000000; font-weight: bold;">set</span> <span style="color: #007800;">$supercache_uri</span> <span style="color: #ff0000;">''</span>;
	<span style="color: #7a0874; font-weight: bold;">}</span>

	<span style="color: #000000; font-weight: bold;">if</span> <span style="color: #7a0874; font-weight: bold;">(</span><span style="color: #007800;">$http_cookie</span> ~<span style="color: #000000; font-weight: bold;">*</span> <span style="color: #ff0000;">"comment_author_|wordpress_logged_|wp-postpass_"</span> <span style="color: #7a0874; font-weight: bold;">)</span> <span style="color: #7a0874; font-weight: bold;">{</span>
		<span style="color: #000000; font-weight: bold;">set</span> <span style="color: #007800;">$supercache_uri</span> <span style="color: #ff0000;">''</span>;
	<span style="color: #7a0874; font-weight: bold;">}</span>

	<span style="color: #000000; font-weight: bold;">if</span> <span style="color: #7a0874; font-weight: bold;">(</span><span style="color: #007800;">$supercache_uri</span> ~ ^<span style="color: #7a0874; font-weight: bold;">(</span>.+<span style="color: #7a0874; font-weight: bold;">)</span>$<span style="color: #7a0874; font-weight: bold;">)</span> <span style="color: #7a0874; font-weight: bold;">{</span>
		<span style="color: #000000; font-weight: bold;">set</span> <span style="color: #007800;">$supercache_file</span> <span style="color: #000000; font-weight: bold;">/</span>wp-content<span style="color: #000000; font-weight: bold;">/</span>cache<span style="color: #000000; font-weight: bold;">/</span>supercache<span style="color: #000000; font-weight: bold;">/</span><span style="color: #007800;">$http_host</span><span style="color: #000000; font-weight: bold;">/</span><span style="color: #007800;">$1</span>index.html;
	<span style="color: #7a0874; font-weight: bold;">}</span>

	<span style="color: #000000; font-weight: bold;">if</span> <span style="color: #7a0874; font-weight: bold;">(</span>-f <span style="color: #007800;">$document_root</span><span style="color: #007800;">$supercache_file</span><span style="color: #7a0874; font-weight: bold;">)</span> <span style="color: #7a0874; font-weight: bold;">{</span>
		rewrite ^<span style="color: #7a0874; font-weight: bold;">(</span>.<span style="color: #000000; font-weight: bold;">*</span><span style="color: #7a0874; font-weight: bold;">)</span>$ <span style="color: #007800;">$supercache_file</span> <span style="color: #7a0874; font-weight: bold;">break</span>;
	<span style="color: #7a0874; font-weight: bold;">}</span>

	<span style="color: #000000; font-weight: bold;">if</span> <span style="color: #7a0874; font-weight: bold;">(</span><span style="color: #000000; font-weight: bold;">!</span>-e <span style="color: #007800;">$request_filename</span><span style="color: #7a0874; font-weight: bold;">)</span> <span style="color: #7a0874; font-weight: bold;">{</span>
		rewrite . <span style="color: #000000; font-weight: bold;">/</span>index.php <span style="color: #c20cb9; font-weight: bold;">last</span>;
	<span style="color: #7a0874; font-weight: bold;">}</span>

	location ~ \.php$ <span style="color: #7a0874; font-weight: bold;">{</span>

		fastcgi_pass   127.0.0.1:<span style="color: #000000;">9000</span>;
		fastcgi_index  index.php;
		fastcgi_param  SCRIPT_FILENAME  <span style="color: #000000; font-weight: bold;">***</span><span style="color: #007800;">$fastcgi_script_name</span>;
		include        fastcgi_params;
	<span style="color: #7a0874; font-weight: bold;">}</span>

	location ~ <span style="color: #000000; font-weight: bold;">/</span>\.ht <span style="color: #7a0874; font-weight: bold;">{</span>
		deny  all;
	<span style="color: #7a0874; font-weight: bold;">}</span>
<span style="color: #7a0874; font-weight: bold;">}</span></pre>
</div>
</div>

下面是解释:

<div class="wp_syntax">
<div class="code">
<pre class="bash" style="font-family: monospace;">gzip_static on;</pre>
</div>
</div>

如果浏览器支持gzip,则在压缩前先寻找是否存在压缩好的同名gz文件避免再次压缩浪费资源,配合supercache的压缩功能一起使用效果最好,相比supercache原生的Apache mod_rewrite实现,nginx的实现简单的多. Apache mod_rewrite足足用了两套看起来一模一样的条件判断来分别rewrite支持gzip压缩和不支持的情况.

<div class="wp_syntax">
<div class="code">
<pre class="bash" style="font-family: monospace;"><span style="color: #000000; font-weight: bold;">if</span> <span style="color: #7a0874; font-weight: bold;">(</span>-f <span style="color: #007800;">$request_filename</span><span style="color: #7a0874; font-weight: bold;">)</span> <span style="color: #7a0874; font-weight: bold;">{</span>
	<span style="color: #7a0874; font-weight: bold;">break</span>;
<span style="color: #7a0874; font-weight: bold;">}</span></pre>
</div>
</div>

//如果是直接请求某个真实存在的文件,则用break语句停止rewrite检查

<div class="wp_syntax">
<div class="code">
<pre class="bash" style="font-family: monospace;"><span style="color: #000000; font-weight: bold;">set</span> <span style="color: #007800;">$supercache_file</span> <span style="color: #ff0000;">''</span>;
<span style="color: #000000; font-weight: bold;">set</span> <span style="color: #007800;">$supercache_uri</span> <span style="color: #007800;">$request_uri</span>;</pre>
</div>
</div>

//用$request_uri初始化变量 $supercache_uri.

<div class="wp_syntax">
<div class="code">
<pre class="bash" style="font-family: monospace;"><span style="color: #000000; font-weight: bold;">if</span> <span style="color: #7a0874; font-weight: bold;">(</span><span style="color: #007800;">$request_method</span> = POST<span style="color: #7a0874; font-weight: bold;">)</span> <span style="color: #7a0874; font-weight: bold;">{</span>
	<span style="color: #000000; font-weight: bold;">set</span> <span style="color: #007800;">$supercache_uri</span> <span style="color: #ff0000;">''</span>;
<span style="color: #7a0874; font-weight: bold;">}</span></pre>
</div>
</div>

//如果请求方式为POST,则不使用supercache.这里用清空$supercache_uri的方法来跳过检测,下面会看到

<div class="wp_syntax">
<div class="code">
<pre class="bash" style="font-family: monospace;"><span style="color: #000000; font-weight: bold;">if</span> <span style="color: #7a0874; font-weight: bold;">(</span><span style="color: #007800;">$query_string</span><span style="color: #7a0874; font-weight: bold;">)</span> <span style="color: #7a0874; font-weight: bold;">{</span>
	<span style="color: #000000; font-weight: bold;">set</span> <span style="color: #007800;">$supercache_uri</span> <span style="color: #ff0000;">''</span>;
<span style="color: #7a0874; font-weight: bold;">}</span></pre>
</div>
</div>

//因为使用了rewrite的原因,正常情况下不应该有query_string(一般只有后台才会出现query string),有的话则不使用supercache

<div class="wp_syntax">
<div class="code">
<pre class="bash" style="font-family: monospace;"><span style="color: #000000; font-weight: bold;">if</span> <span style="color: #7a0874; font-weight: bold;">(</span><span style="color: #007800;">$http_cookie</span> ~<span style="color: #000000; font-weight: bold;">*</span> <span style="color: #ff0000;">"comment_author_|wordpress_logged_|wp-postpass_"</span> <span style="color: #7a0874; font-weight: bold;">)</span> <span style="color: #7a0874; font-weight: bold;">{</span>
	<span style="color: #000000; font-weight: bold;">set</span> <span style="color: #007800;">$supercache_uri</span> <span style="color: #ff0000;">''</span>;
<span style="color: #7a0874; font-weight: bold;">}</span></pre>
</div>
</div>

//默认情况下,supercache是仅对unknown user使用的.其他诸如登录用户或者评论过的用户则不使用.

comment_author是测试评论用户的cookie, wordpress_logged是测试登录用户的cookie. wp-postpass不大清楚,字面上来看可能是曾经发表过文章的?只要cookie中含有这些字符串则条件成立.

原来的写法中检测登录用户cookie用的是wordpress_,但是我在测试中发现登入/登出以后还会有一个叫wordpress_test_cookie存在,不知道是什么作用,我也不清楚一般用户是否会产生这个cookie.由于考虑到登出以后这个cookie依然存在可能会影响到cache的判断,于是把这里改成了匹配wordpress_logged_

<div class="wp_syntax">
<div class="code">
<pre class="bash" style="font-family: monospace;"><span style="color: #000000; font-weight: bold;">if</span> <span style="color: #7a0874; font-weight: bold;">(</span><span style="color: #007800;">$supercache_uri</span> ~ ^<span style="color: #7a0874; font-weight: bold;">(</span>.+<span style="color: #7a0874; font-weight: bold;">)</span>$<span style="color: #7a0874; font-weight: bold;">)</span> <span style="color: #7a0874; font-weight: bold;">{</span>
	<span style="color: #000000; font-weight: bold;">set</span> <span style="color: #007800;">$supercache_file</span> <span style="color: #000000; font-weight: bold;">/</span>wp-content<span style="color: #000000; font-weight: bold;">/</span>cache<span style="color: #000000; font-weight: bold;">/</span>supercache<span style="color: #000000; font-weight: bold;">/</span><span style="color: #007800;">$http_host</span><span style="color: #007800;">$1</span>index.html;
<span style="color: #7a0874; font-weight: bold;">}</span></pre>
</div>
</div>

//如果变量$supercache_uri不为空,则设置cache file的路径

这里稍微留意下$http_host$1index.html这串东西,其实写成 $http_host/$1/index.html 就好懂很多

以这个rewrite形式的url为例

> cafeneko.info/2010/09/tsukihime-doujin_part01/

其中

$http_host = ‘cafeneko.info’ , $1 = $request_uri = ‘/2010/09/tsukihime-doujin_part01/’

则 $http_host$1index.html = ‘cafeneko.info/2010/09/tsukihime-doujin_part01/index.html’

而 $http_host/$1/index.html = ‘cafeneko.info//2010/09/tsukihime-doujin_part01//index.html’

虽然在调试过程中两者并没有不同,不过为了保持正确的路径,还是省略了中间的/符号.

最后上例rewrite后的url = ‘cafeneko.info/wp-content/cache/supercache/cafeneko.info/2010/09/tsukihime-doujin_part01/index.html’

<div class="wp_syntax">
<div class="code">
<pre class="bash" style="font-family: monospace;"><span style="color: #000000; font-weight: bold;">if</span> <span style="color: #7a0874; font-weight: bold;">(</span>-f <span style="color: #007800;">$document_root</span><span style="color: #007800;">$supercache_file</span><span style="color: #7a0874; font-weight: bold;">)</span> <span style="color: #7a0874; font-weight: bold;">{</span>
	rewrite ^<span style="color: #7a0874; font-weight: bold;">(</span>.<span style="color: #000000; font-weight: bold;">*</span><span style="color: #7a0874; font-weight: bold;">)</span>$ <span style="color: #007800;">$supercache_file</span> <span style="color: #7a0874; font-weight: bold;">break</span>;
<span style="color: #7a0874; font-weight: bold;">}</span></pre>
</div>
</div>

//检查cache文件是否存在,存在的话则执行rewrite,留意这里因为是rewrite到html静态文件,所以可以直接用break终止掉.

<div class="wp_syntax">
<div class="code">
<pre class="bash" style="font-family: monospace;"><span style="color: #000000; font-weight: bold;">if</span> <span style="color: #7a0874; font-weight: bold;">(</span><span style="color: #000000; font-weight: bold;">!</span>-e <span style="color: #007800;">$request_filename</span><span style="color: #7a0874; font-weight: bold;">)</span> <span style="color: #7a0874; font-weight: bold;">{</span>
	rewrite . <span style="color: #000000; font-weight: bold;">/</span>index.php <span style="color: #c20cb9; font-weight: bold;">last</span>;
<span style="color: #7a0874; font-weight: bold;">}</span></pre>
</div>
</div>

//执行到此则说明不使用suercache,进行wordpress的permalink rewrite

检查请求的文件/目录是否存在,如果不存在则条件成立, rewrite到index.php

顺便说一句,当时这里这句rewrite看的我百思不得其解. .

只能匹配一个字符啊?这是什么意思?

一般情况下,想调试nginx rewrite最简单的方法就是把flag写成redirect,这样就能在浏览器地址栏里看到真实的rewrite地址.

然而对于permalink rewrite却不能用这种方法,因为一旦写成redirect以后,不管点什么链接,只要没有supercache,都是跳转回首页了.

后来看了一些文章才明白了rewrite的本质,其实是在保持请求地址不变的情况下,在服务器端将请求转到特定的页面.

乍一看supercache的性质有点像302到静态文件,所以可以用redirect调试.

但是permalink却是性质完全不同的rewrite,这跟wordpress的处理方式有关. 我研究不深就不多说了,简单说就是保持URL不变将请求rewrite到index.php,WP将分析其URL结构再对其并进行匹配(文章,页面,tag等),然后再构建页面. 所以其实这条rewrite

<div class="wp_syntax">
<div class="code">
<pre class="bash" style="font-family: monospace;">rewrite . <span style="color: #000000; font-weight: bold;">/</span>index.php <span style="color: #c20cb9; font-weight: bold;">last</span>;</pre>
</div>
</div>

说的是,任何请求都会被rewrite到index.php.因为”.”匹配任意字符,所以这条rewrite其实可以写成任何形式的能任意命中的正则.比如说

<div class="wp_syntax">
<div class="code">
<pre class="bash" style="font-family: monospace;">rewrite . <span style="color: #000000; font-weight: bold;">/</span>index.php <span style="color: #c20cb9; font-weight: bold;">last</span>;
rewrite ^ <span style="color: #000000; font-weight: bold;">/</span>index.php <span style="color: #c20cb9; font-weight: bold;">last</span>;
rewrite .<span style="color: #000000; font-weight: bold;">*</span> <span style="color: #000000; font-weight: bold;">/</span>index.php <span style="color: #c20cb9; font-weight: bold;">last</span>;</pre>
</div>
</div>

效果都是一样的,都能做到permalink rewrite.

最后要提的就是有人可能注意到我的rewrite规则是放在server块中的.网上能找到的大多数关于wordpress的nginx rewrite规则都是放在location /下面的,但是上面我却放在了server块中,为何?

原因是WP或某个插件会在当前页面做一个POST的XHR请求,本来没什么特别,但问题就出在其XHR请求的URL结构上.

正常的permalink一般为: domain.com/year/month/postname/ 或者 domain.com/tags/tagname/ 之类.

但这个XHR请求的URL却是 domain.com/year/month/postname/index.php 或者 domain.com/tags/tagname/index.php

这样一来就命中了location ~ \.php$而交给fastcgi,但因为根本没有做过rewrite其页面不可能存在,结果就是这个XHR返回一个404

鉴于location之间匹配优先级的原因,我将主要的rewrite功能全部放进了server区块中,这样就得以保证在进行location匹配之前是一定做过rewrite的.

这时有朋友又要问了,为什么命中的是location ~ \.php$而不是location / ?

…望天…长叹…这就要扯到天杀的location匹配问题了….

locatoin并非像rewrite那样逐条执行,而是有着匹配优先级的,当一条请求同时满足几个location的匹配时,其只会选择其一的配置执行.

其寻找的方法为:

> 1\. 首先寻找所有的常量匹配,如location /, location /av/, 以相对路径自左向右匹配,匹配长度最高的会被使用,
> 
> 2\. 然后按照配置文件中出现的顺序依次测试正则表达式,如 location ~ download\/$, location ~* \.wtf, 第一个匹配会被使用
> 
> 3\. 如果没有匹配的正则,则使用之前的常量匹配

而下面几种方法当匹配时会立即终止其他location的尝试

> 1\. = 完全匹配,location = /download/
> 
> 2\. ^~ 终止正则测试,如location ^~ /download/ 如果这条是最长匹配,则终止正则测试,这个符号只能匹配常量
> 
> 3\. 在没有=或者^~的情况下,如果常量完全匹配,也会立即终止测试,比如请求为 /download/ 会完全命中location /download/而不继续其他的正则测试

总结:

> 1\. 如果完全匹配(不管有没有=),尝试会立即终止
> 
> 2\. 以最长匹配测试各个常量,如果常量匹配并有 ^~, 尝试会终止
> 
> 3\. 按在配置文件中出现的顺序测试各个正则表达式
> 
> 4\. 如果第3步有命中,则使用其匹配location,否则使用第2步的location

另外还可以定义一种特殊的named location,以@开头,如location @thisissparta 不过这种location定义不用于一般的处理,而是专门用于try_file, error_page的处理,这里不再深入.

晕了没? 用前文的例子来看看

<div class="wp_syntax">
<div class="code">
<pre class="bash" style="font-family: monospace;">location  = <span style="color: #000000; font-weight: bold;">/</span> <span style="color: #7a0874; font-weight: bold;">{</span>
  ....配置A
<span style="color: #7a0874; font-weight: bold;">}</span>

location  <span style="color: #000000; font-weight: bold;">/</span> <span style="color: #7a0874; font-weight: bold;">{</span>
  ....配置B
<span style="color: #7a0874; font-weight: bold;">}</span>

location ^~ <span style="color: #000000; font-weight: bold;">/</span>images<span style="color: #000000; font-weight: bold;">/</span> <span style="color: #7a0874; font-weight: bold;">{</span>
  ....配置C
<span style="color: #7a0874; font-weight: bold;">}</span>

location ~<span style="color: #000000; font-weight: bold;">*</span> \.<span style="color: #7a0874; font-weight: bold;">(</span>gif<span style="color: #000000; font-weight: bold;">|</span>jpg<span style="color: #000000; font-weight: bold;">|</span>jpeg<span style="color: #7a0874; font-weight: bold;">)</span>$ <span style="color: #7a0874; font-weight: bold;">{</span>
  ....配置D
<span style="color: #7a0874; font-weight: bold;">}</span></pre>
</div>
</div>

访问 / 会使用配置A -&gt; 完全命中

访问 /documents/document.html 会使用配置B -&gt; 匹配常量B,不匹配正则C和D,所以用B

访问 /images/1.gif 会使用配置C -&gt; 匹配常量B,匹配正则C,使用首个命中的正则,所以用C

访问 /documents/1.jpg 会使用配置D -&gt; 匹配常量B,不匹配正则C,匹配正则D,使用首个命中的正则,所以用D

那么再回头看我们刚才说的问题.为什么那个URL结果奇怪的XHR请求会命中location ~ \.php$而不是location / ? 我相信你应该已经知道答案了.

所以要解决这个问题最简单的方法就是把rewrite规则放在比location先执行的server块里面就可以了哟.

这次的研究笔记就到此为止了.

最后留一个思考题,如果不将rewrite规则放入server块,还有什么方法可以解决这个XHR 404的问题?

原来的location /块包含从location ~ \.php$到root为止的部分.

答案是存在的.在用使用目前的方法前我死脑筋的在保留location /的前提下尝试了很多种方法…请不要尝试为各种permalink构建独立的location.因为wp的permalink种类很多,包括单篇文章,页面,分类,tag,作者,存档等等..欢迎在回复中讨论 /

> 参考:
> 
> [Nginx wiki](http://wiki.nginx.org)

-EOF-

&nbsp;

更新  @2010.10.23

之前的supercache rewrite规则适用于大部分的WP.但是并不适用于mobile press插件的移动设备支持.

因为其中并没有检测移动设备的user agent,从而导致移动设备也会被rewrite到cache上.这样的结果是在移动设备上也是看到的跟PC一样的完全版blog. 对于性能比较好的手机比如iphone安卓什么的大概没什么问题,但比较一般的比如nokia上用opera mini等看就会比较辛苦了,这次把supercache原本在htaccess中的移动设备检测的代码块也移植了过来.

在前文的配置文件中cookie检测后面加入以下代码段

<div class="wp_syntax">
<div class="code">
<pre class="bash" style="font-family: monospace;">	<span style="color: #666666; font-style: italic;"># Bypass special user agent</span>
	<span style="color: #000000; font-weight: bold;">if</span> <span style="color: #7a0874; font-weight: bold;">(</span><span style="color: #007800;">$http_user_agent</span> ~<span style="color: #000000; font-weight: bold;">*</span> <span style="color: #ff0000;">"2.0 MMP|240x320|400X240|AvantGo|BlackBerry|Blazer|Cellphone|Danger|DoCoMo|Elaine/3.0|EudoraWeb|Googlebot-Mobile|hiptop|IEMobile|KYOCERA/WX310K|LG/U990|MIDP-2.|MMEF20|MOT-V|NetFront|Newt|Nintendo Wii|Nitro|Nokia|Opera Mini|Palm|PlayStation Portable|portalmmm|Proxinet|ProxiNet|SHARP-TQ-GX10|SHG-i900|Small|SonyEricsson|Symbian OS|SymbianOS|TS21i-10|UP.Browser|UP.Link|webOS|Windows CE|WinWAP|YahooSeeker/M1A1-R2D2|iPhone|iPod|Android|BlackBerry9530|LG-TU915 Obigo|LGE VX|webOS|Nokia5800"</span><span style="color: #7a0874; font-weight: bold;">)</span> <span style="color: #7a0874; font-weight: bold;">{</span>
		<span style="color: #000000; font-weight: bold;">set</span> <span style="color: #007800;">$supercache_uri</span> <span style="color: #ff0000;">''</span>;
	<span style="color: #7a0874; font-weight: bold;">}</span>

	<span style="color: #000000; font-weight: bold;">if</span> <span style="color: #7a0874; font-weight: bold;">(</span><span style="color: #007800;">$http_user_agent</span> ~<span style="color: #000000; font-weight: bold;">*</span> <span style="color: #ff0000;">"w3c |w3c-|acs-|alav|alca|amoi|audi|avan|benq|bird|blac|blaz|brew|cell|cldc|cmd-|dang|doco|eric|hipt|htc_|inno|ipaq|ipod|jigs|kddi|keji|leno|lg-c|lg-d|lg-g|lge-|lg/u|maui|maxo|midp|mits|mmef|mobi|mot-|moto|mwbp|nec-|newt|noki|palm|pana|pant|phil|play|port|prox|qwap|sage|sams|sany|sch-|sec-|send|seri|sgh-|shar|sie-|siem|smal|smar|sony|sph-|symb|t-mo|teli|tim-|tosh|tsm-|upg1|upsi|vk-v|voda|wap-|wapa|wapi|wapp|wapr|webc|winw|winw|xda\ |xda-"</span><span style="color: #7a0874; font-weight: bold;">)</span> <span style="color: #7a0874; font-weight: bold;">{</span>
		<span style="color: #000000; font-weight: bold;">set</span> <span style="color: #007800;">$supercache_uri</span> <span style="color: #ff0000;">''</span>;
	<span style="color: #7a0874; font-weight: bold;">}</span></pre>
</div>
</div>

这样就可以对移动设备绕开cache规则,而直接使用mobile press产生的移动版的效果了.

<script type="text/javascript">// < ![CDATA[
// < ![CDATA[
AKPC_IDS += "431,";
// ]]&gt;</script>