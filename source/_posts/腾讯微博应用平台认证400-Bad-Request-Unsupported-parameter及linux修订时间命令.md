title: "腾讯微博应用平台认证400 Bad Request: Unsupported parameter及linux修订时间命令"
tags:
  - linux
  - solution
date: 2011-09-24 11:33:59
---

开发微博应用，本地测试ok，但是在服务器上始终第一次Oauth授权请求返回 400 Bad Request: Unsupported parameter，弄的很郁闷，后来去微博应用平台的论坛搜了下 400，出现了一个可能的答案

> 服务器时间与标准时间不同步，快了或慢了都可能出现这个问题。

于是上服务器看了下时间（date命令），与标准时间快了12分钟，使用

> 如果是校对时间，一般用ntpdate来对时
> 
> sudo ntpdate 210.72.145.44
> 
> 这个是国家授时中心的时间服务器的地址，不怎么好记
> 
> 一般是用time.windows.com， ntp.ubuntu.com
> 
> 或
> 
> sudo date -s HH:SS
> 
> sudo date -s MM/DD/YY
> 
> sudo date -s hh:mm

赶紧试了下，果然解决问题。囧！

——————————————————————————————————————

服务器果然经常性时间加快，今天又快了10分钟，出现认证错误，于是搜索自动同步，用到计划任务.

计划任务设置方法另见：[Linux下的计划任务设置](http://cssor.com/ubuntu-linux-planning-task-establishment-use-crontab-e.html)。

<pre class="brush:shell">sudo crontab -e #编辑计划任务
#打开的文件中添加一行：
0 0 * * * /usr/sbin/ntpdate ntp.ubuntu.com;/sbin/hwclock -w
#即每天0点同步一次时间，Ctrl+o保存,Ctrl+x选择no退出
service cron restart
</pre>