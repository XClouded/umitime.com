title: js生成uuid的方法
tags:
  - JavaScript
  - js uuid
  - uuid
date: 2014-05-28 17:00:05
---

找到三种生成uuid方法，如下：

<span id="more-1090"></span>

<pre>/*!
Math.uuid.js (v1.4)

http://www.broofa.com

mailto:robert@broofa.com

Copyright (c) 2010 Robert Kieffer
Dual licensed under the MIT and GPL licenses.
*/

/*
 * Generate a random uuid.
 *
 * USAGE: Math.uuid(length, radix)
 *   length - the desired number of characters
 *   radix  - the number of allowable values for each character.
 *
 * EXAMPLES:
 *   // No arguments  - returns RFC4122, version 4 ID
 *   &gt;&gt;&gt; Math.uuid()
 *   "92329D39-6F5C-4520-ABFC-AAB64544E172"
 *
 *   // One argument - returns ID of the specified length
 *   &gt;&gt;&gt; Math.uuid(15)     // 15 character ID (default base=62)
 *   "VcydxgltxrVZSTV"
 *
 *   // Two arguments - returns ID of the specified length, and radix. (Radix must be &lt;= 62)
 *   &gt;&gt;&gt; Math.uuid(8, 2)  // 8 character ID (base=2)
 *   "01001010"
 *   &gt;&gt;&gt; Math.uuid(8, 10) // 8 character ID (base=10)
 *   "47473046"
 *   &gt;&gt;&gt; Math.uuid(8, 16) // 8 character ID (base=16)
 *   "098F4D35"
 */
(function() {
  // Private array of chars to use
  var CHARS = '0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz'.split('');

  Math.uuid = function (len, radix) {
    var chars = CHARS, uuid = [], i;
    radix = radix || chars.length;

    if (len) {
      // Compact form
      for (i = 0; i &lt; len; i++) uuid[i] = chars[0 | Math.random()*radix];
    } else {
      // rfc4122, version 4 form
      var r;

      // rfc4122 requires these characters
      uuid[8] = uuid[13] = uuid[18] = uuid[23] = '-';
      uuid[14] = '4';

      // Fill in random data.  At i==19 set the high bits of clock sequence as
      // per rfc4122, sec. 4.1.5
      for (i = 0; i &lt; 36; i++) {
        if (!uuid[i]) {
          r = 0 | Math.random()*16;
          uuid[i] = chars[(i == 19) ? (r &amp; 0x3) | 0x8 : r];
        }
      }
    }

    return uuid.join('');
  };

  // A more performant, but slightly bulkier, RFC4122v4 solution.  We boost performance
  // by minimizing calls to random()
  Math.uuidFast = function() {
    var chars = CHARS, uuid = new Array(36), rnd=0, r;
    for (var i = 0; i &lt; 36; i++) {
      if (i==8 || i==13 ||  i==18 || i==23) {
        uuid[i] = '-';
      } else if (i==14) {
        uuid[i] = '4';
      } else {
        if (rnd &lt;= 0x02) rnd = 0x2000000 + (Math.random()*0x1000000)|0;
        r = rnd &amp; 0xf;
        rnd = rnd &gt;&gt; 4;
        uuid[i] = chars[(i == 19) ? (r &amp; 0x3) | 0x8 : r];
      }
    }
    return uuid.join('');
  };

  // A more compact, but less performant, RFC4122v4 solution:
  Math.uuidCompact = function() {
    return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function(c) {
      var r = Math.random()*16|0, v = c == 'x' ? r : (r&amp;0x3|0x8);
      return v.toString(16);
    });
  };
})();</pre>

使用uuidFast效率更高些。来源于：http://www.cnblogs.com/greengnn/archive/2011/10/06/2199719.html

2014年08月04日 再增加一种方法，源于backbone.localStorage.js，胜在代码量够少：

<pre>
// Generate four random hex digits.
function S4() {
   return (((1+Math.random())*0x10000)|0).toString(16).substring(1);
};
// Generate a pseudo-GUID by concatenating random hexadecimal.
function guid() {
   return (S4()+S4()+"-"+S4()+"-"+S4()+"-"+S4()+"-"+S4()+S4()+S4());
};
</pre>

加个简介（源自https://zh.wikipedia.org/zh-cn/全局唯一标识符）：

全局唯一标识符，简称GUID（发音为 /ˈɡuːɪd/或/ˈɡwɪd/），是一种由算法生成的唯一标识，通常表示成32个16进制数字（0－9，A－F）组成的字符串，如：{21EC2020-3AEA-1069-A2DD-08002B30309D}，它实质上是一个128位长的二进制整数。GUID一词有时也专指微软对UUID标准的实现。

GUID的主要目的是产生完全唯一的数字。在理想情况下，任何计算机和计算机集群都不会生成两个相同的GUID。GUID的总数也足够大，达到了2128（3.4×1038）个，所以随机生成两个相同GUID的可能性是非常小的，但并不为0。所以，用于生成GUID的算法通常都加入了非随机的参数（如时间），以保证这种重复的情况不会发生。