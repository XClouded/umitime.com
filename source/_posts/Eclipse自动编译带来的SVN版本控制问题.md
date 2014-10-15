title: Eclipse自动编译带来的SVN版本控制问题
tags:
  - solution
date: 2011-11-04 10:38:53
---

项目中，SVN通过每个文件夹下隐藏的.svn文件夹来进行版本控制。

Eclipse的源码文件夹src自然需要svn控制，而eclipse开启自动编译的话，默认src下的所以文件及文件夹都会被编译到classes文件夹中去，这时候.svn文件夹也被copy到了classes文件夹中。

而这个.svn文件夹保存的是src版本信息而非classes版本信息，.svn被克隆了， 在classes进行svn提交发现提示所有的.java源码missing，如果提交了，相当于在src删除了所有的源码文件。

这个缺陷对不知道情况的人会比较严重。解决方法就是修改eclipse的build path，排除掉.svn文件夹的自动编译。

右键项目名 -&gt; 选择Build Path，选择Configure Build Path -&gt; 选择Source，找到该项目下的Excluded项，编辑，添加排除规则： **/.svn/**

最后保存即可。

找图看这里： http://www.360doc.com/content/11/0729/12/2631212_136507208.shtml

&nbsp;