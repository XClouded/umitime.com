title: 被忽视的servlet filter配置参数dispatcher
tags:
  - java
  - solution
  - dispatcher
  - filter
  - sevlet filter
date: 2011-12-12 16:54:31
---

最近项目遇到一种情况，偶坚持index.jsp采用forward跳转到action，而另一位同事发现这种方式没进入他写的一个filter，导致出现一些未被过滤的问题，于是被改成redirect方式，以便进入过滤器。

访问首页被redirect，懂的自然懂，相当于每次访问首页发了两次请求，对于首页这种访问量相当大的页面，相当不利。

其实我们都忽视了filter一个配置参数：dispatcher，因为默认filter只过滤拦截redirect request ， 对于forward（服务器端跳转）不处理。而dispatcher就是解决问题之道。

dispatcher的描述如下：

Element : dispatcher

Data Type : string

Enumerated Values :

- FORWARD

- INCLUDE

- REQUEST

- ASYNC

- ERROR

可用值有5个，有经验的一看就明白，所以知道这些以后，项目里面的问题可以很容易解决掉，只需要配置如下即可：

<pre class="brush:xml">&lt;filter-mapping&gt;
    &lt;filter-name&gt;test&lt;/filter-name&gt;
    &lt;url-pattern&gt;/*&lt;/url-pattern&gt;
    &lt;dispatcher&gt;REQUEST&lt;/dispatcher&gt;
    &lt;dispatcher&gt;FORWARD&lt;/dispatcher&gt;
    &lt;!-- 可以配置多个dispatcher --&gt;
&lt;/filter-mapping&gt;</pre>