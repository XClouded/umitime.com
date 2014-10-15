title: 传送门：使用Varnish代替Squid做网站缓存加速器的详细解决方案
tags:
  - 拓展
  - 服务器
  - squid
  - Varnish
date: 2012-06-10 15:03:16
---

> 使用Varnish代替Squid的理由有三点：
> 
> 1、Varnish采用了“Visual Page Cache”技术，在内存的利用上，Varnish比Squid具有优势，它避免了Squid频繁在内存、磁盘中交换文件，性能要比Squid高。
> 
> 2、Varnish的稳定性还不错，我管理的一台图片服务器运行Varnish已经有一个月，没有发生过故障，而进行相同工作的Squid服务器就倒过几次。
> 
> 3、通过Varnish管理端口，可以使用正则表达式快速、批量地清除部分缓存，这一点是Squid不能具备的。

了解了解：http://blog.s135.com/post/313/