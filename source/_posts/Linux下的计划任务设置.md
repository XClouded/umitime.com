title: Linux下的计划任务设置
tags:
  - linux
  - crontab -e
  - linux计划任务
  - ubuntu crontab
  - 定时任务
date: 2012-04-15 17:40:12
---

### Linux下如何编写计划任务crontab。

输入crontab -e 编辑crontab 计划任务，Ubuntu里默认编辑器是Nano，如何使用Nano可以参考Nano 快捷键。

# m h dom mon dowcommand

#分 时 日 月 周 执行命令

分钟[0,59]，小时[0,23]，日期[1,31] ，月[1,12]，周[0,6]，可以使用的参数有“*”，“/”，“-”和“，”。

* 表示任意取值范围内数字 在分钟里*表示0到59的任意数字

- 表示选择区域范围内的数字 3-10表示3-10的任意数字，包含3和10

, 选择数字 2,4,5 选择2，4，5

/ 每多少数字 在分钟里 */2表示每二分钟

例子：

<span id="more-701"></span>

每1分钟执行一次command

* * * * * command

每小时的第3和第15分钟执行

3,15 * * * *command

在上午8点到11点的第3和第15分钟执行

3,15 8-11 * * * command

每隔两天的上午8点到11点的第3和第15分钟执行

3,15 8-11 */2 * * command

每个星期一的上午8点到11点的第3和第15分钟执行

3,15 8-11 * * 1 command

crontab可以定时执行任务，在Ubuntu下输入crontab帮助如下

test@ubuntu:~$crontab

crontab: usageerror: file name must be specified for replace

crontab [-u user]file

crontab [-u user]{ -e | -l | -r }

-e (edit user&#8217;scrontab)

-l (list user&#8217;scrontab)

-r (delete user&#8217;scrontab)

编辑完计划任务以后，重启服务使生效。可以用以下命令：

<pre class="brush:shell">#Ubuntu推荐
service cron restart
#或者
/etc/init.d/cron restart</pre>

**相关资料链接：**

http://hi.baidu.com/yuzao/blog/item/ecf5a7510105200b377abe7b.html