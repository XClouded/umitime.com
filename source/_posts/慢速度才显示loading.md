title: 慢速度才显示loading
tags:
  - thinking
  - 前端优化
  - loading
date: 2014-07-30 15:39:19
---

Loading提示应该是广泛也普通的应用了，其意义都明白。

这里我想说的是，在资源载入速度很快的情境下，loading提示往往一闪而过，所以其实是没必要显示loading的。这应该是可以优化的一个点。

如何优化呢？假设提供loading的两个方法：loading() 和 loaded()，只需loading设置一个比较理想延迟的setTimeout，loaded里面清除timeoutId即可。