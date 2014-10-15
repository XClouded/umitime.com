title: ubuntu开关机命令
tags:
  - linux
date: 2011-09-26 15:45:00
---

**1、关机命令 shutdown**

好像ubuntu的终端中默认的是当前用户的命令，只是普通用户，因此在终端器中可以使用sudo -sh 转换到管理员root用户下执行命令。

1)shutdown &#8211;help

可以查看shutdown命令如何使用，当然也可以使用man shutdown命令。

2） shutdown -h now 现在立即关机

3）shutdown -r now 现在立即重启

4）shutdown -r +3 三分钟后重启

5）shutdown -h +3 &#8220;The System will shutdown after 3 minutes&#8221; 提示使用者将在三分钟后关机

6）shutdown -r 20:23 在20：23时将重启计算机

7）shutdown -r 20:23 &amp; 可以将在20：23时重启的任务放到后台去，用户可以继续操作终端

**2、中断命令**

1）执行完命令后（若没有转到后台），可以使用CTRL+C来中止命令

2）kill 程序编码

当命令在后台执行时系统会返回一个程序编码，例如：在使用 shutdown -r 20:23 &amp; 系统返回系统编码：4905

我们可以使用kill 4905来中止重启命令

3ps aux | grep 程序编号

我们如果知道程序的编号，我们可以使用ps aux |grep 程序编号来查询该程序编号对应的命令是什么。

例如上面的程序编号为4905 ，我们可以使用ps aux | grep 4905 就可以查到 shutdown -r 20:23 &amp; 的命令

halt—-最简单的关机命令

其实halt就是调用shutdown -h。halt执行时﹐杀死应用进程﹐执行sync系统调用﹐文件系统写操作完成后就会停止内核。

参数说明:

[-n] 防止sync系统调用﹐它用在用fsck修补根分区之后﹐以阻止内核用老版本的超级块〔superblock〕覆盖修补过的超级块。

[-w] 并不是真正的重启或关机﹐只是写wtmp〔/var/log/wtmp〕纪录。

[-d] 不写wtmp纪录〔已包含在选项[-n]中〕。

[-f] 没有调用shutdown而强制关机或重启。

[-i] 关机〔或重启〕前﹐关掉所有的网络接口。

[-p] 该选项为缺省选项。就是关机时调用poweroff。

init

init是所有进程的祖先﹐它的进程号始终为1﹐所以发送TERM信号给init会终止所有的用户进程﹑守护进程等。shutdown 就是使用这种机制。init定义了8个运行级别(runlevel)，init 0为关机﹐init 1为重启。关于init可以长篇大论﹐这里就不再叙述。另外还有telinit命令可以改变init的运行级别﹐比如﹐telinit -iS可使系统进入单用户模式﹐并且得不到使用shutdown时的信息和等待时间。

**3、注销系统的logout命令**

Logout 注销是登陆的相对操作，登陆系统后，若要离开系统，用户只要直接下达logout命令即可:

[root@localhost root]#logout

Red Hat Linuxrelease 9(Shike)

Kernel 2.4.20.8 on an i686

Login: ← 回到登陆的画面