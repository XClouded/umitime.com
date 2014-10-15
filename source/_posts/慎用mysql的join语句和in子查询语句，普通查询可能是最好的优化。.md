title: 慎用mysql的join语句和in子查询语句，普通查询可能是最好的优化。
tags:
  - database
  - solution
  - in子查询慢
  - mysql in
  - mysql优化
date: 2012-02-29 15:35:56
---

服务器mysql集群，表引擎ndbcluster。

其中查3张表，原语句为：

<pre class="brush:sql">select user.uid,user.uname,user.idcard_status,user_introduction.user_pic
from user left join user_introduction on user.uid = user_introduction.uid
where user.uid in(select uid from participate_class where cid = '11428' and paystatus = 1) ;</pre>

表数据总共9K+条左右的数据 ，所以说数据并不多，但是这个sql的执行时间让人惊讶： 35sec~36sec。其中in子查询拆分来查都很快，一旦组合起来就慢的要死。

<span id="more-616"></span>

explain的情况如下：

[![mysql explain select](http://cssor.com/wp-content/uploads/2012/02/mysql-explain-select.png "mysql explain select")](http://cssor.com/wp-content/uploads/2012/02/mysql-explain-select.png)

刚好跟一篇文章中描述的一样：http://hi.baidu.com/yu_zhou2000/blog/item/b9edb26716d93b3baa184c64.html。

这里也是in的条件如果是固定常量会非常快，比如 in (1,2,3)，但是如果向上面的in(select &#8230;) 写法就巨慢无比。这个文章也写了个解决方案，换成自己的案例写法就应该是下面这样：

<pre class="brush:sql">select user.uid,user.uname,user.idcard_status,user_introduction.user_pic
from user left join user_introduction on user.uid = user_introduction.uid
where user.uid in(select uid from
   (select uid from participate_class where cid = '11413' and paystatus = 1) as tbl
) ;</pre>

这样的写法的确有效，查询执行时间降低到0.109sec左右。看explain，查询了更多的行，但是时间是降低 了：

[![](http://cssor.com/wp-content/uploads/2012/02/sub-join-explain-300x53.png "嵌套子查询explain")](http://cssor.com/wp-content/uploads/2012/02/sub-join-explain.png)

如果说实话，我接触sql的次数并不多，甚至对join的理解都不深刻，目前还搞不清left,inner,right是怎样的情况。正因为如此，我更偏向于写普通的多表查询语句，于是改上面的语句为：

<pre class="brush:sql">select user.uid,user.uname,user.idcard_status,user_introduction.user_pic
from user, user_introduction, participate_class
where user.uid = participate_class.uid and user.uid = user_introduction.uid
and participate_class.cid = '11418' and participate_class.paystatus = 1 ;</pre>

很普通的语句，多了很多where条件而已，但是执行效率得到更大的改观，仅用了0.015sec左右，偶尔飙升到0.030sec。explain分析如下：

[![](http://cssor.com/wp-content/uploads/2012/02/mysql-simple-selectpng.png "mysql simple selectpng")](http://cssor.com/wp-content/uploads/2012/02/mysql-simple-selectpng.png)

貌似ndb引擎本身对join语句支持不太好 ，不过我本身也不喜欢join（或许说是不太会），而in语句出现了这样的问题。所以个人感觉还是两者都慎用，但不是禁用，能用普通多表查询就用多表查询即可。

这本身是个小案例，具体事件具体分析，后来看到说mysql的设计就是基于join的： “在 MySQL 的 SELECT 查询当中，其核心算法就是 JOIN 查询算法”，来源：http://blog.sina.com.cn/s/blog_4550f3ca0100vkfx.html

所以还是需要深入研究的，最好是还交给dba的好。