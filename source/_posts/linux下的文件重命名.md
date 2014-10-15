title: linux下的文件重命名
tags:
  - linux
  - WordPress
  - linux重命名
  - rename
  - ubuntu
  - 重命名
date: 2010-08-15 00:11:48
---

linux下对文件重命名有两种命令： mv  ，rename

#### mv很简单，move文件移动

mv /dir/file1  /dir2/file1

两个参数，第一个是源文件，第二个是目的地，如果第二个参数文件名不一样，则会重命名。

当两个参数不带目录，只有文件名时，那就是重命名了。这是单个文件的重命名。

#### rename  arg1  arg2  arg3

rename才是真正的批量重命名命令。而且他是3个参数，不是2个。

arg1：旧的字符串

arg2：新的字符串

arg3：匹配要重命名的文件，可以使用3种通配符，*、?、[char]，*表示任意多个字符，?表示单个字符，[char]匹配char单个自定的精确字符，可以填写任意字符，foo[a]*表示只匹配fooa开头的文件名，如果一个文件是foobcc.txt，是不会被匹配的。

值的注意的是，此命令在不同的linux版本也有不同，Debian一系的操作系统别有用法。举例说明：

比如/home下有两个文件 abbcc.txt, addbb.txt , a.txt

我想把a替换为xxx，命令是这样的 ： rename &#8220;a&#8221; &#8220;xxx&#8221; *.txt

那么它会首先去匹配有哪些文件需要修改，这里凡是.txt后缀的文件都会被匹配，如果改成?.txt则只会匹配到一个文件，那就是a.txt。然后把匹配到的文件中的a字符替换为xxx，注意测试时abab.txt这样的，只会替换第一个a，有待再了解。

说到Debian一系的操作系统，比如Ubuntu，这个命令这样使用是不对的，报错，向下面这样的：

Bareword &#8220;a&#8221; not allowed while &#8220;strict subs&#8221; in use at (eval 1) line 1.

经过Google之后发现有这样的说法：

> On Debian-based distros it takes a perl expression and a list of files. you need to would need to use:
> 
> rename &#8216;s/foo/foox/&#8217; *

这里是一个perl表达式，好理解点说就是综合了前两个参数为1个，这样就只需要2个参数，而非上面所说的3个参数形式。

所以在Ubuntu下执行上面举例的重命名时，命令是这样的：rename &#8216;s/a/xxx/&#8217; *.txt

由于没有深入应用，今天是群里有人问了，于是自己动手实践了一下，总结上面简单的用法，也算以备后忘吧。