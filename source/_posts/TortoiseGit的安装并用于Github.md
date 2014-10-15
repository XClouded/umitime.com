title: TortoiseGit的安装并用于Github
tags:
  - 拓展
  - git
  - github
  - msysgit
  - tortoisegit
date: 2012-08-27 17:30:11
---

TortoiseGit安装还是很方便容易的，关键是用SSH方式连接Github需要生产key比较麻烦些。在网上找了一些，都是命令行生成key之类，看着迷迷糊糊的。后来看到Github有https方式，也算解决了连接问题，只是每次push和pull必须填GitHub的账号和密码进行验证。

不死心，又google了几次，换了几次关键词，总算找到一个靠谱点的，顺手转过来，就不重复劳动了，直接借用成果了。以下是正文。

<span id="more-957"></span>

来源于： [http://www.fantxi.com/blog/archives/tortoisegit-github/](http://www.fantxi.com/blog/archives/tortoisegit-github/)

装了GIT很久了，最近也没时间鼓捣，今天提交GitHub时发现不太会用了，所以查查资料，简单记录下[git](http://www.fantxi.com/blog/tag/git/ "git")的安装和使用。

#### 1\. msysgit

TortoiseGit依赖msysgit，下载：http://code.google.com/p/msysgit/downloads/list

有Portable版，我用的就是这个。比如解压.7z到：D:\WebDevelopment\Git\PortableGit

#### 2\. 配置TortoiseGit

下载：http://code.google.com/p/tortoisegit/downloads/list

安装TortoiseGit，全部保持默认即可。

配置git的路径：

桌面右键 -TortoiseGit &#8211; setting &#8211; General &#8211; MSysGit路径，指定：

D:\WebDevelopment\Git\PortableGit\bin。

创建ssh key：

开始菜单-TortoiseGit-Puttygen， Generate，然后Save private key到本地 (无需key passphrase)，比如保存到：D:\WebDevelopment\Git。保存的key下次比如重装系统, 可以继续使用。复制对话框里生成的ssh key，类似：

ssh-rsa

&#8230;..

&#8230;..

&#8230;..= rsa-key-20120218

TortoiseGit &#8211; setting &#8211; Git &#8211; Config，填写 name、email。

#### 3\. 使用GitHub

GitHub功能类似googlecode，方便储存代码。主页，进入Edit Your Profile，找到SSH Keys，Add New SSH Key或edit现有的SSH Key。title随便取，把上面复制的ssh key粘贴到key一栏。

创建Putty项目：

主页，New repository创建项目，填写项目名称之类的。创建好后，可以看到一些说明，找到里面的git@github.com:yourName/test.git，复制这个URL。

本地创建个文件夹，比如test，右键-Git Clone，URL中粘贴刚才复制的URL。Load Putty Key选择上面保存到本地的ssh key。OK，弄完了。

#### 提交code到github

右键-Git Commit，然后Push，就可以推到GitHub里面了。

<!--more-->

其他的:

fetch: 从repository中下载代码。

pull: 将repository代码fetch并且merge到当前分支（相当于:fetch+merge）。

clean up: 删除无版本控制的文件

add: 增加到版本库

create branch: 创建分支

switch/checkout: 切换分支

show log: 查看分支及修改记录

#### 重装系统继续使用key

Puttygen, Actions &#8211; Load, 找到: ssh-rsa_key.ppk

找到之前创建的GIT项目, 然后Pull可能会看到错误,比如 git did not exit cleanly (exit code 128)之类的。

当Push的时候应该会看到更详细的错误，比如：Github提示：Please audit your SSH keys， 并且返回一个网址。

打开网址，可选Reject/Approve，选择Approve，就OK了。现在可以继续Pull/Push了.