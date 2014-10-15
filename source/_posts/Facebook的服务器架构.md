title: Facebook的服务器架构
tags:
  - 拓展
date: 2011-08-17 15:27:51
---

来自： http://www.webguo.com/2011/06/14/facebook_arch.html

#### 大体层次划分

Facebook的架构可以从不同角度来换分层次。

一种是：一边是PHP整的经典的LAMP stack；另外一个是非PHP整的各种service。

[![](http://www.cssor.com/wp-content/uploads/2011/08/10187_1307608455Yy4U.jpg "lamp and services")](http://www.cssor.com/wp-content/uploads/2011/08/10187_1307608455Yy4U.jpg)

Facebook的页面从刚创立的时候扎克伯格写的，到现在，都用PHP开发。后端有用各种语言开发的service。它们之间用跨语言的thrift RPC通信(Scribe也是建立在Thrift之上)。<span id="more-320"></span>

另外一个角度划分的层次是：

[![](http://www.cssor.com/wp-content/uploads/2011/08/10187_13076084569w99.jpg "10187_13076084569w99")](http://www.cssor.com/wp-content/uploads/2011/08/10187_13076084569w99.jpg)

前面是负载局衡器(没说是用硬件的还是软件的)；负责分配 前端的Web服务器， Web服务器是用PHP来聚合数据；最后面是 Services，Memcached和数据库。

有意思的是对后面三种的定性：

Services – 快速，复杂； 自己开发的业务进程，来实现复杂的业务逻辑，速度快。

Memchached – 快速，简单；Memchached做简单的key-value缓存，服务应用快速的读请求。

数据库 – 缓慢，持久。数据库做持久存储，磁盘IO自然慢，不过有memcached做缓存没关系。

**NewsFeed的架构**

写：

Bob更新状态，Web服务器上的PHP程序除了将内容到MySQL数据库之外，也将该行为动态的ID通过Scribe发到一个Leaf Server上(根据Bob的用户ID选的Leaf Server)。

[![](http://www.cssor.com/wp-content/uploads/2011/08/10187_1307608456Zw32.jpg "10187_1307608456Zw32")](http://www.cssor.com/wp-content/uploads/2011/08/10187_1307608456Zw32.jpg)

读：

另一个人Alice打开Facebook，加载主页，PHP程序向Aggregator服务器查询(Thrift调用)，Aggregator从若干个Leaf Server里头读出Alice的朋友的所有行为动态/action的前四十个，aggregator做聚合和一定的排序，返回给PHP程序。

[![](http://www.cssor.com/wp-content/uploads/2011/08/10187_1307608457VpWT.jpg "10187_1307608457VpWT")](http://www.cssor.com/wp-content/uploads/2011/08/10187_1307608457VpWT.jpg)

PHP程序获得这些行为动态的ID之后，从Memcached中读出这些ID对应的内容，如Memcached没有则从MySQL数据库中读，汇聚后生成HTML返回给浏览器。

**Chat的架构**

页面请求，仍WEB服务器处理(PHP)处理，当然也依赖web tier之后的各种Service。比如查看消息历史啊，在线用户列表啊，发送聊天消息啊。

接收聊天消息，则没通过PHP服务器，而是专用的用Erlang写的Channel服务器来处理，通过long-polling来接收聊天消息。Channel服务器是Chat服务的核心部件。发送的消息通过web tier发到Channel服务器。

后方有用C++写的chatlogger服务器来做历史记录的读写。

同样也用C++写了presence服务器来从channel服务器汇集在线状态。

系统的简化结构如下图所示：

[![](http://www.cssor.com/wp-content/uploads/2011/08/6_110610091427_1.jpg "6_110610091427_1")](http://www.cssor.com/wp-content/uploads/2011/08/6_110610091427_1.jpg)

Web tier, chatlogger, presence, channel 都是多个服务器组成的集群。

Channel服务器有根据User ID做分区，每个分区由一个高可用的Channel集群服务。

Web tier, chatlogger, presence，在公开的文章和PPT中并没说这些集群具体怎么做分布和冗余备份的。