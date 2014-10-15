title: Ubuntu解压打包命令全集
tags:
  - linux
date: 2011-09-24 11:50:44
---

<div>来源于： http://remindme.blogbus.com/logs/25006959.html</div>
<div></div>
<div>**<span style="font-size: x-small;"><span style="color: #0000ff;">.tar</span></span>**</p>

解包：tar xvf FileName.tar

打包：tar cvf FileName.tar DirName

<span style="color: #008000;">（注：tar是打包，不是压缩！）</span>

&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;

**<span style="font-size: x-small;"><span style="color: #0000ff;">.gz</span></span>**

解压1：gunzip FileName.gz

解压2：gzip -d FileName.gz

压缩：gzip FileName

**<span style="font-size: x-small;"><span style="color: #0000ff;">.tar.gz 和 .tgz</span></span>**

解压：tar zxvf FileName.tar.gz

压缩：tar zcvf FileName.tar.gz DirName

&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;

**<span style="font-size: x-small;"><span style="color: #0000ff;">.bz2</span></span>**

解压1：bzip2 -d FileName.bz2

解压2：bunzip2 FileName.bz2

压缩： bzip2 -z FileName

**<span style="font-size: x-small;"><span style="color: #0000ff;">.tar.bz2</span></span>**

解压：tar jxvf FileName.tar.bz2

压缩：tar jcvf FileName.tar.bz2 DirName

&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;

**<span style="font-size: x-small;"><span style="color: #0000ff;">.bz</span></span>**

解压1：bzip2 -d FileName.bz

解压2：bunzip2 FileName.bz

压缩：<span style="color: #ffa500;">未知</span>

**<span style="font-size: x-small;"><span style="color: #0000ff;">.tar.bz</span></span>**

解压：tar jxvf FileName.tar.bz

压缩：<span style="color: #ffa500;">未知</span>

&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;

**<span style="font-size: x-small;"><span style="color: #0000ff;">.Z</span></span>**

解压：uncompress FileName.Z

压缩：compress FileName

**<span style="font-size: x-small;"><span style="color: #0000ff;">.tar.Z</span></span>**

解压：tar Zxvf FileName.tar.Z

压缩：tar Zcvf FileName.tar.Z DirName

&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;

**<span style="font-size: x-small;"><span style="color: #0000ff;">.zip</span></span>**

解压：unzip FileName.zip

压缩：zip FileName.zip DirName

&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;

**<span style="font-size: x-small;"><span style="color: #0000ff;">.rar</span></span>**

解压：rar x FileName.rar

压缩：rar a FileName.rar DirName

<span style="color: #008000;">

rar请到：[<span style="color: #003793;">http://www.rarsoft.com/download.htm</span>](http://www.rarsoft.com/download.htm) 下载！

解压后请将rar_static拷贝到/usr/bin目录（其他由$PATH环境变量指定的目录也可以）：

[root@www2 tmp]# cp rar_static /usr/bin/rar</span>

&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;

**<span style="font-size: x-small;"><span style="color: #0000ff;">.lha</span></span>**

解压：lha -e FileName.lha

压缩：lha -a FileName.lha FileName

<span style="color: #008000;">

lha请到：[<span style="color: #003793;">http://www.infor.kanazawa-it.ac.jp/~ishii/lhaunix/</span>](http://www.infor.kanazawa-it.ac.jp/%7Eishii/lhaunix/)下载！

&gt;解压后请将lha拷贝到/usr/bin目录（其他由$PATH环境变量指定的目录也可以）：

[root@www2 tmp]# cp lha /usr/bin/</span>

&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;

**<span style="font-size: x-small;"><span style="color: #0000ff;">.rpm</span></span>**

解包：rpm2cpio FileName.rpm | cpio -div

&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;

**<span style="font-size: x-small;"><span style="color: #0000ff;">.deb</span></span>**

解包：ar p FileName.deb data.tar.gz | tar zxf -

&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;

**<span style="font-size: x-small;"><span style="color: #0000ff;">.tar .tgz .tar.gz .tar.Z

.tar.bz .tar.bz2 .zip .cpio .rpm .deb .slp .arj .rar .ace .lha .lzh

.lzx .lzs .arc .sda .sfx .lnx .zoo .cab .kar .cpt .pit .sit .sea</span></span>**

解压：sEx x FileName.*

压缩：sEx a FileName.* FileName

<span style="color: #008000;">

sEx只是调用相关程序，本身并无压缩、解压功能，请注意！

sEx请到： [<span style="color: #003793;">http://sourceforge.net/projects/sex</span>](http://sourceforge.net/projects/sex)下载！

解压后请将sEx拷贝到/usr/bin目录（其他由$PATH环境变量指定的目录也可以）：

[root@www2 tmp]# cp sEx /usr/bin/</span></div>
<div></div>
<div>解压后编译：

sudo ./configure

make

make install
</div>
<div></div>
<div>当然，编译的时候必须要以root 用户，而解压的时候不一定，普通用户就何以。以上是常用的命令，一下是不常用的命令，在这里提下，给兄弟们一个映象，到时候能看懂就可以了。</div>
<div></div>
<div>方法二：</p>

sudo ./configure &#8211;prefix=/路径

make ; make install&nbsp;

方法三：

sudo ./configure &#8211;enable-static-link \

&#8211;prefix=$XXX/static &#8211;with-curses &amp;&amp;

make &amp;&amp;

make install

命令解释

&#8211;enable-static-link: 这个配置命令使bash被静态链接。

&#8211;prefix=$XXX/static:这个配置命令把Bash的所有文件安装到$XXX/static目录下，这个目录在chroot环境下或在最终的XXX系统中将成为 /static 目录。(XXX为用户）

&#8211;with-curses: 将bash链接到某一个库，正如LFS系统将它指向static这一个库。

其实静态链接的bash并不一定要链接到libncurses (也可以暂时链接到静态的termcap ）但在LFS中不能这样做(关于LFS的方法可访问相关网站，此问题不在这一范围内）。

每行最后的&amp;&amp;用处很大，它使后一个命令仅在前一个命令返回值为0(表示正确执行)的情况下才执行。在所有的命令都是拷贝

&amp;粘贴的情况下，我们必须保证如果./configure出错，make就不执行，同样地，如果 make 出错，make

install就不执行，诸如此类。

以上方法三是构建LFS(linux from scratch 从网上直接下载源代码，从头编写程序的方式)系统并编译软件的重要方法之一，事实上Linux系统包括在Unix系统中在cmd的上是共通的。

</div>