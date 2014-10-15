title: 实用而不墨守成规-Git Flow
tags:
  - 拓展
  - git
date: 2014-07-30 11:44:45
---

> **据说 Google和Facebook的Git库，代码只有最新版(trunk)**<span style="color: #222222;">，没有 release 版本，没有版本号。</span>

其实我很能理解，像我们的项目也不是按照[特定git flow](http://cssor.com/a-successful-git-branching-model.html "更好的使用Git方法推荐-Git分支管理策略")(初学者应该借鉴git flow)进行管理的，根据实际情况来使用，这就是实用。

对我们的项目来讲，基本有用的分支就是master和test，develop基本废弃，而master和test是互不干涉的，test可以随意删除和重建，各类新功能从master开出分支，合并到test测试，最后合并到master上线。develop或许有用的地方就是成为开出test分支的基础分支。

引用来自：http://fex.baidu.com/blog/2014/03/G-ossip/