title: Mac osx保存和恢复tmux会话
tags:
  - linux
  - tmux session
date: 2014-09-28 12:29:17
---

系统重启，tmux会话就会丢失，需要重开tmux，不爽，于是去问Google大神怎么办，得到一个手动保存和恢复的方法。源于：http://superuser.com/a/615716

该方法作者在他的[github库](https://github.com/mislav/dotfiles)中提供了很多脚本，我只取相关的[tmux-seesion](#tmux-session-script)脚本拿来用用，具体使用方法如下：

<span id="more-1257"></span>

1、设立一个用户专用的命令目录，比如我的是 /Users/cssor/bin，将该目录加入系统环境变量，并设置可执行权限

<pre>
#将下面的语句加入到 ~/.bash_profile (使用zsh的，则为 ~/.zshrc)
export PATH=$PATH:/Users/cssor/bin
</pre>
<pre>
#设置可执行权限
chmod -R +x /User/cssor/bin
</pre>

2、保存脚本到该目录，文件名为 tmux-session，完整路径为 /Users/cssor/bin/tmux-session。脚本内容如下

<pre>
#!/usr/bin/env bash
# Save and restore the state of tmux sessions and windows.
# TODO: persist and restore the state &#038; position of panes.
set -e

dump() {
  local d=$'\t'
  tmux list-windows -a -F "#S${d}#W${d}#{pane_current_path}"
}

save() {
  dump > ~/.tmux-session
}

terminal_size() {
  stty size 2>/dev/null | awk '{ printf "-x%d -y%d", $2, $1 }'
}

session_exists() {
  tmux has-session -t "$1" 2>/dev/null
}

add_window() {
  tmux new-window -d -t "$1:" -n "$2" -c "$3"
}

new_session() {
  cd "$3" &#038;&#038;
  tmux new-session -d -s "$1" -n "$2" $4
}

restore() {
  tmux start-server
  local count=0
  local dimensions="$(terminal_size)"

  while IFS=$'\t' read session_name window_name dir; do
    if [[ -d "$dir" &#038;&#038; $window_name != "log" &#038;&#038; $window_name != "man" ]]; then
      if session_exists "$session_name"; then
        add_window "$session_name" "$window_name" "$dir"
      else
        new_session "$session_name" "$window_name" "$dir" "$dimensions"
        count=$(( count + 1 ))
      fi
    fi
  done < ~/.tmux-session

  echo "restored $count sessions"
}

case "$1" in
save | restore )
  $1
  ;;
* )
  echo "valid commands: save, restore" >&#038;2
  exit 1
esac
</pre>

原链接获取：https://github.com/mislav/dotfiles/blob/d2af5900fce38238d1202aa43e7332b20add6205/bin/tmux-session

3、保存和恢复会话的使用方式：

<pre>
#会话信息会保存到 ~/.tmux-session
#手动保存可能会遗忘，可以加入cron定时任务自动保存
tmux-session save
#系统重启后，恢复：
tmux-session restore
</pre>

4、设置定时任务自动保存会话信息

<pre>
#编辑定时任务
crontab -e
</pre>

在打开的编辑器（一般是vim）中，输入：

<pre>
#表示每59分钟执行一次，可按自己的习惯改为 0-59 之间的数字，更多用法需自行学习crontab
59 * * * * tmux-session save
</pre>

保存后退出，看见 crontab: installing new crontab 即表示成功。

我使用的时候，默认编辑器是vi，返回错误： crontab: &#8220;/usr/bin/vi&#8221; exited with status 1.

这是据说因为vi编辑的不行，vim却可以（==! 一群乌鸦从脑袋上飞过~）。

执行 export EDITOR=vim 可以修正这个问题，或尝试 export EDITOR=nano.

查看成功的任务： crontab -l

资料： [关于crontab](http://cssor.com/ubuntu-linux-planning-task-establishment-use-crontab-e.html "Linux下的计划任务设置")