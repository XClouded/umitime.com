title: ubuntu下tomcat的启动管理命令脚本
tags:
  - linux
  - 服务器
  - tomcat init.d
  - tomcat manage
  - ubuntu tomcat
date: 2012-02-29 11:26:35
---

同事收集各种资料写的一个比较完善的tomcat服务器管理脚本，比之前的管理要好很多，启动停止会先检查tomcat进程情况，目前用来没有发现问题。

将该脚本保存到/etc/init.d/tomcat7 即可，使用方法：

<pre class="brush:shell">/etc/init.d/tomcat7 start
/etc/init.d/tomcat7 stop
/etc/init.d/tomcat7 restart</pre>

脚本内容如下，需要替换相应目录为实际目录：

&nbsp;

<span id="more-610"></span>

<pre class="brush:shell">#!/bin/bash
### BEGIN INIT INFO
# Provides:          tomcat6
# Required-Start:    $local_fs $remote_fs $network
# Required-Stop:     $local_fs $remote_fs $network
# Should-Start:      $named
# Should-Stop:       $named
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Short-Description: Start Tomcat.
# Description:       Start the Tomcat servlet engine.
### END INIT INFO

TOMCAT_USER=tomcat
TOMCAT_GROUP=tomcat
TOMCAT_HOME=/usr/share/tomcat7
TOMCAT_BIN=$TOMCAT_HOME/bin
TOMCAT_TEMP=$TOMCAT_HOME/temp
TOMCAT_LOCK=/var/run/tomcat.lock
JAVA_HOME=/usr/lib/jvm/java-6-sun/
TOMCAT_UMASK=002

if [ `id -u` -ne 0 ]; then
        echo "You need root or sudo privileges to run this script"
        exit 1
fi

start_sams() {
        if [ -f $TOMCAT_LOCK ];then
                echo ' * tomcat has already been started or has problems'
                echo ' * Please contact wewe'
                exit 1
        fi
        start-stop-daemon --start -u "$TOMCAT_USER" -g "$TOMCAT_GROUP" \
                -c "$TOMCAT_USER" -d "$TOMCAT_TEMP" \
                -k "$TOMCAT_UMASK" -x "$TOMCAT_BIN/startup.sh" &gt; /dev/null &amp;&amp; \
                echo " * tomcat starts successfully" &amp;&amp;  touch $TOMCAT_LOCK
}

stop_sams() {
        if [ ! -f $TOMCAT_LOCK ];then
                echo ' * tomcat has already been stopped or has problems'
                echo ' * Please contact wewe'
                exit 1
        fi
        start-stop-daemon --stop -u "$TOMCAT_USER" -g "$TOMCAT_GROUP" \
                -c "$TOMCAT_USER" -d "$TOMCAT_TEMP" \
                -k "$TOMCAT_UMASK" "$TOMCAT_BIN/shutdown.sh" &amp;&amp; \
                echo " * tomcat stops successfully" &amp;&amp; rm -f $TOMCAT_LOCK
}

case "$1" in
        start)
                start_sams
        ;;
        stop)
                stop_sams
        ;;
        restart)
                stop_sams
                sleep 10
                start_sams
        ;;
        *)
                echo ' * Usage: /etc/init.d/tomcat7 {start|stop|restart}'
                exit 1
        ;;
esac</pre>