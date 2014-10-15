title: Nginx设置非标准MIME类型后缀开启SSI
tags:
  - 服务器
  - nginx
  - ssi
date: 2013-02-01 15:08:35
---

最近遇到的是为 .less文件开启SSI， nginx下需要改两个地方：

1、修改nginx目录中的mime.types文件，修改

text/html  html htm shtml less;

2、然后在虚拟机配置中添加

    location ~* \.(shtml|html|less)$ {

        ssi on;

        ssi_silent_errors on;

    }

这是为less添加的例子，其他非标准MIME类型可以以此方式添加。

之所以把less添加为 text/html ，因为有个ssi指令  ssi_types 默认为text/html