title: jQuery 1.4.3 form bug
tags:
  - 自然
  - bug
  - id
  - jQuery
  - name
date: 2010-12-30 16:20:26
---

这个版本，当Form中存在name=&#8221;id&#8221;时候， form对象成为[form#[object HTMLInputElement]](注：这个是input type=&#8221;hidden&#8221; name=&#8221;id&#8221;的情况)， 而且form.find()下面的元素时候找不到了，你将find nothing。困惑了半天，去Google了一下“jquery id name=&#8221;id&#8221;”， 发现官方有报这个bug了（原来早被人发现了。。。囧），

bug地址：http://bugs.jquery.com/ticket/7324

所以升级到1.4.4版本吧，这样就好了。