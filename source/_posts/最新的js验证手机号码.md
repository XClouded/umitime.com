title: 最新的js验证手机号码
tags:
  - JavaScript
  - 手机号码段
  - 手机号验证
date: 2013-11-29 02:02:20
---

号码段不断更新，老的一些验证正则已经不行了，截止到文章发出时间，最新的规则如下：

<pre>var regex = {
mobile: /^0?(13[0-9]|15[012356789]|18[0-9]|14[57]|170)[0-9]{8}$/
};</pre>

该规则根据http://www.pooy.net/js-preg.html改动，因为他的规则也已经过期了。

附带最新数据信息（http://duanmu.org/log/mobile/）：

程序所带ACCESS数据库为2013年11月17日更新，数据来源IP138+百度，数据总数【286764】条

电信133 -&gt; 9648 电信153 -&gt; 9818 电信180 -&gt; 9577 电信189 -&gt; 9988

电信181 -&gt; 8263 移动134 -&gt; 9694 移动135 -&gt; 10000 移动136 -&gt; 10000

移动137 -&gt; 9976 移动138 -&gt; 9994 移动139 -&gt; 9998 移动150 -&gt; 10000

移动151 -&gt; 10000 移动152 -&gt; 10000 移动157 -&gt; 7096 移动158 -&gt; 10000

移动159 -&gt; 10000 移动182 -&gt; 9960 移动183 -&gt; 9975 移动184 -&gt; 4879

移动187 -&gt; 9980 移动188 -&gt; 9546 联通130 -&gt; 9879 联通131 -&gt; 10000

联通132 -&gt; 9804 联通155 -&gt; 10000 联通156 -&gt; 9781 联通185 -&gt; 6983

联通186 -&gt; 9684 数据卡145 -&gt; 5883 数据卡147 -&gt; 6358

2014年06月26日 新增170号码段。