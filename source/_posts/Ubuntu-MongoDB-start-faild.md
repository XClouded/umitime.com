title: Ubuntu MongoDB start faild
tags:
  - linux
  - solution
date: 2011-09-20 10:31:25
---

本地服务器直接拔了电源，然后再去重启mongodb的时候就不行了。

<pre class="brush:shell">:~$ sudo service mongodb start
mongodb start/running, process 1990</pre>

看着回显似乎ok，然后

<pre class="brush:shell">:~$ mongo
MongoDB shell version: 1.8.3
connecting to: test
Tue Sep 20 09:38:51 Error: couldn't connect to server 127.0.0.1 shell/mongo.js:79
exception: connect failed</pre>

再然后：

<pre class="brush:shell">:~$ ps -def | grep mongod
gaoren    2046  1824  0 09:43 pts/0    00:00:00 grep --color=auto mongod</pre>

网上都没说回显是什么，偶看着觉得还正常，但是pid=2046 在只运行这个命令 ps -def 的情况下没有看见。

这不是很囧吗。后来在国外一个社区得到了提示，想起来非法关闭服务器，有一个lock文件需要清理掉才行，于是看了下mongodb.log日志， 在最后：

<pre class="brush:xml">Tue Sep 20 09:31:38 [initandlisten] User Assertion: 12596:old lock file
Tue Sep 20 09:31:38 [initandlisten] exception in initAndListen std::exception: old lock file, terminating
Tue Sep 20 09:31:38 dbexit:
Tue Sep 20 09:31:38 [initandlisten] shutdown: going to close listening sockets...
Tue Sep 20 09:31:38 [initandlisten] shutdown: going to flush diaglog...
Tue Sep 20 09:31:38 [initandlisten] shutdown: going to close sockets...
Tue Sep 20 09:31:38 [initandlisten] shutdown: waiting for fs preallocator...
Tue Sep 20 09:31:38 [initandlisten] shutdown: closing all files...
Tue Sep 20 09:31:38 closeAllFiles() finished
Tue Sep 20 09:31:38 dbexit: really exiting now</pre>

果然是因为old lock file的存在导致无法启动，启动的时候居然也不报错，囧，不经常非正常关闭服务器的话，还真是想不到会是这个原因，搞了好久。

最后 sudo rm  /var/lib/mongodb/mongod.lock 就行了。