title: html5 localStorage
tags:
  - JavaScript
  - html5
  - localStorage
  - sessionStorage
date: 2012-08-28 11:29:34
---

来自https://developer.mozilla.org/en-US/docs/DOM/Storage

从IE8+，到目前标准浏览器都支持window.localStorage对象，并且一般也都支持window.JSON，这样很好， 因为localStorage存的都是字符串，如果是json格式，刚好很方便用JSON对象处理。

<span id="more-960"></span>

localStorage API如下：

<pre>interface Storage {
  readonly attribute unsigned long length;
  [IndexGetter] DOMString key(in unsigned long index);
  [NameGetter] DOMString getItem(in DOMString key);
  [NameSetter] void setItem(in DOMString key, in DOMString data);
  [NameDeleter] void removeItem(in DOMString key);
  void clear();
};</pre>

同时一个localStorage变量storage也支持属性直接读写，比如：

<pre>storage.a = 'a';
storage['b'] = 'b';</pre>

不支持localStorage的浏览器，可以用cookie来兼容一下，当然cookie存储大小上限一般是4K，兼容如下：

<pre>if (!window.localStorage) {
  window.localStorage = {
    getItem: function (sKey) {
      if (!sKey || !this.hasOwnProperty(sKey)) { return null; }
      return unescape(document.cookie.replace(new RegExp("(?:^|.*;\\s*)" + escape(sKey).replace(/[\-\.\+\*]/g, "\\$&amp;") + "\\s*\\=\\s*((?:[^;](?!;))*[^;]?).*"), "$1"));
    },
    key: function (nKeyId) {
      return unescape(document.cookie.replace(/\s*\=(?:.(?!;))*$/, "").split(/\s*\=(?:[^;](?!;))*[^;]?;\s*/)[nKeyId]);
    },
    setItem: function (sKey, sValue) {
      if(!sKey) { return; }
      document.cookie = escape(sKey) + "=" + escape(sValue) + "; expires=Tue, 19 Jan 2038 03:14:07 GMT; path=/";
      this.length = document.cookie.match(/\=/g).length;
    },
    length: 0,
    removeItem: function (sKey) {
      if (!sKey || !this.hasOwnProperty(sKey)) { return; }
      document.cookie = escape(sKey) + "=; expires=Thu, 01 Jan 1970 00:00:00 GMT; path=/";
      this.length--;
    },
    hasOwnProperty: function (sKey) {
      return (new RegExp("(?:^|;\\s*)" + escape(sKey).replace(/[\-\.\+\*]/g, "\\$&amp;") + "\\s*\\=")).test(document.cookie);
    }
  };
  window.localStorage.length = (document.cookie.match(/\=/g) || window.localStorage).length;
}</pre>

跟localStorage一样的，还有个sessionStorage，顾名思义就是数据存储有效期等于session的有效期。

更多了解见这里： http://www.cnblogs.com/xiaowei0705/archive/2011/04/19/2021372.html