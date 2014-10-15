title: HTML5 XMLHttpRequest中的新功能
tags:
  - JavaScript
  - XMLHttpRequest
date: 2014-06-20 15:57:35
---

来源于：http://www.html5rocks.com/zh/tutorials/file/xhr2/

## 简介

HTML5 世界中有这样一位无名英雄：`XMLHttpRequest`。严格地说，XHR2 并不属于 HTML5。不过，它是浏览器供应商对于核心平台不断做出的改进中的一部分。我之所以将 XHR2 加入我们新的百宝囊中，就是因为它在如今复杂的网络应用中扮演了不可或缺的角色。

结果呢，我们这位老朋友来了个大变身，很多人都不知道它的新功能了。[2 级 XMLHttpRequest](http://dev.w3.org/2006/webapi/XMLHttpRequest-2/) 引入了大量的新功能（例如跨源请求、上传进度事件以及对上传/下载二进制数据的支持等），一举封杀了我们网络应用中的疯狂黑客。这使得 AJAX 可以与很多尖端的 HTML5 API 结合使用，例如 [File System API](/tutorials/file/filesystem/)、[Web Audio API](http://chromium.googlecode.com/svn/trunk/samples/audio/specification/specification.html) 和 WebGL。

此教程重点介绍 `XMLHttpRequest` 中的新功能，尤其是可用于[处理文件](/tutorials/file/dndfiles/)的功能。

<span id="more-1112"></span>

&nbsp;

## 抓取数据

以前通过 XHR 抓取二进制 blob 形式的文件是很痛苦的事情。从技术上来说，这甚至是不可能的实现。有一种广为流传的一种技巧，是将 MIME 类型替换为由用户定义的字符集，如下所示：

提取图片的旧方法：

<pre class="prettyprint"><span class="kwd">var</span><span class="pln"> xhr </span><span class="pun">=</span><span class="kwd">new</span><span class="typ">XMLHttpRequest</span><span class="pun">();</span><span class="pln">
xhr</span><span class="pun">.</span><span class="pln">open</span><span class="pun">(</span><span class="str">'GET'</span><span class="pun">,</span><span class="str">'/path/to/image.png'</span><span class="pun">,</span><span class="kwd">true</span><span class="pun">);</span><span class="com">// Hack to pass bytes through unprocessed.</span>**<span class="pln">xhr</span><span class="pun">.</span><span class="pln">overrideMimeType</span><span class="pun">(</span><span class="str">'text/plain; charset=x-user-defined'</span><span class="pun">);</span>**<span class="pln">

xhr</span><span class="pun">.</span><span class="pln">onreadystatechange </span><span class="pun">=</span><span class="kwd">function</span><span class="pun">(</span><span class="pln">e</span><span class="pun">)</span><span class="pun">{</span><span class="kwd">if</span><span class="pun">(</span><span class="kwd">this</span><span class="pun">.</span><span class="pln">readyState </span><span class="pun">==</span><span class="lit">4</span><span class="pun">&amp;&amp;</span><span class="kwd">this</span><span class="pun">.</span><span class="pln">status </span><span class="pun">==</span><span class="lit">200</span><span class="pun">)</span><span class="pun">{</span><span class="kwd">var</span><span class="pln"> binStr </span><span class="pun">=</span><span class="kwd">this</span><span class="pun">.</span><span class="pln">responseText</span><span class="pun">;</span><span class="kwd">for</span><span class="pun">(</span><span class="kwd">var</span><span class="pln"> i </span><span class="pun">=</span><span class="lit">0</span><span class="pun">,</span><span class="pln"> len </span><span class="pun">=</span><span class="pln"> binStr</span><span class="pun">.</span><span class="pln">length</span><span class="pun">;</span><span class="pln"> i </span><span class="pun">&lt;</span><span class="pln"> len</span><span class="pun">;</span><span class="pun">++</span><span class="pln">i</span><span class="pun">)</span><span class="pun">{</span>**<span class="kwd">var</span><span class="pln"> c </span><span class="pun">=</span><span class="pln"> binStr</span><span class="pun">.</span><span class="pln">charCodeAt</span><span class="pun">(</span><span class="pln">i</span><span class="pun">);</span>****<span class="com">//String.fromCharCode(c &amp; 0xff);</span>****<span class="kwd">var</span><span class="kwd">byte</span><span class="pun">=</span><span class="pln"> c </span><span class="pun">&amp;</span><span class="lit">0xff</span><span class="pun">;</span><span class="com">// byte at offset i</span>**<span class="pun">}</span><span class="pun">}</span><span class="pun">};</span><span class="pln">

xhr</span><span class="pun">.</span><span class="pln">send</span><span class="pun">();</span></pre>

虽然这种方法可行，但是 `responseText` 中实际返回的并不是二进制 blob，而是代表图片文件的二进制字符串。我们要巧妙地让服务器在不作处理的情况下，将这些数据传递回去。虽然这个技巧有用，但是我不推荐大家走这种歪门邪道。只要是通过玩弄字符代码和字符串操控技巧，强行将数据转化成所需的格式，都会出现问题。

### 指定响应格式

在前一个示例中，我们通过替换服务器的 MIME 类型并将响应文本作为二进制字符串处理，下载了二进制“文件”形式的图片。现在，让我们利用 `XMLHttpRequest` 新增的 `responseType` 和 `response` 属性，告知浏览器我们希望返回什么格式的数据。

<dl>
<dt>xhr.**responseType**</dt>
<dt></dt>
<dd>在发送请求前，根据您的数据需要，将 `xhr.responseType` 设置为“text”、“arraybuffer”、“blob”或“document”。请注意，设置（或忽略）`xhr.responseType = ''` 会默认将响应设为“text”。</dd>
<dt>xhr.**response**</dt>
<dd>成功发送请求后，xhr 的响应属性会包含 `DOMString`、`ArrayBuffer`、`Blob` 或 `Document` 形式（具体取决于 `responseTyp ` 的设置）的请求数据。</dd>
</dl>

凭借这个优秀的新属性，我们可以修改上一个示例：以 `ArrayBuffer` 而非字符串的形式抓取图片。将缓冲区移交给 `BlobBuilder` API 可创建 `Blob`：

<pre class="prettyprint"><span class="typ">BlobBuilder</span><span class="pun">=</span><span class="pln"> window</span><span class="pun">.</span><span class="typ">MozBlobBuilder</span><span class="pun">||</span><span class="pln"> window</span><span class="pun">.</span><span class="typ">WebKitBlobBuilder</span><span class="pun">||</span><span class="pln"> window</span><span class="pun">.</span><span class="typ">BlobBuilder</span><span class="pun">;</span><span class="kwd">var</span><span class="pln"> xhr </span><span class="pun">=</span><span class="kwd">new</span><span class="typ">XMLHttpRequest</span><span class="pun">();</span><span class="pln">
xhr</span><span class="pun">.</span><span class="pln">open</span><span class="pun">(</span><span class="str">'GET'</span><span class="pun">,</span><span class="str">'/path/to/image.png'</span><span class="pun">,</span><span class="kwd">true</span><span class="pun">);</span>**<span class="pln">xhr</span><span class="pun">.</span><span class="pln">responseType </span><span class="pun">=</span><span class="str">'arraybuffer'</span><span class="pun">;</span>****<span class="pln">xhr</span><span class="pun">.</span><span class="pln">onload</span>**<span class="pun">=</span><span class="kwd">function</span><span class="pun">(</span><span class="pln">e</span><span class="pun">)</span><span class="pun">{</span><span class="kwd">if</span><span class="pun">(</span><span class="kwd">this</span><span class="pun">.</span><span class="pln">status </span><span class="pun">==</span><span class="lit">200</span><span class="pun">)</span><span class="pun">{</span><span class="kwd">var</span><span class="pln"> bb </span><span class="pun">=</span><span class="kwd">new</span><span class="typ">BlobBuilder</span><span class="pun">();</span><span class="pln">
    bb</span><span class="pun">.</span><span class="pln">append</span><span class="pun">(</span>**<span class="kwd">this</span><span class="pun">.</span><span class="pln">response</span>**<span class="pun">);</span><span class="com">// Note: not xhr.responseText</span><span class="kwd">var</span><span class="pln"> blob </span><span class="pun">=</span><span class="pln"> bb</span><span class="pun">.</span><span class="pln">getBlob</span><span class="pun">(</span><span class="str">'image/png'</span><span class="pun">);</span><span class="pun">...</span><span class="pun">}</span><span class="pun">};</span><span class="pln">

xhr</span><span class="pun">.</span><span class="pln">send</span><span class="pun">();</span></pre>

好多了！

#### ArrayBuffer 响应

[`ArrayBuffer`](https://developer.mozilla.org/en/JavaScript_typed_arrays/ArrayBuffer) 是二进制数据通用的固定长度容器。如果您需要原始数据的通用缓冲区，ArrayBuffer 就非常好用，但是它真正强大的功能是让您使用 [JavaScript 类型数组](https://developer.mozilla.org/en/JavaScript_typed_arrays)创建底层数据的“视图”。实际上，可以通过单个 `ArrayBuffer` 来源创建多个视图。例如，您可以创建一个 8 位整数数组，与来自相同数据的现有 32 位整数数组共享同一个 `ArrayBuffer`。底层数据保持不变，我们只是创建其不同的表示方法。

例如，下面以 `ArrayBuffer` 的形式抓取我们相同的图片，但是现在，会通过该数据缓冲区创建无符号的 8 位整数数组。

<pre class="prettyprint"><span class="kwd">var</span><span class="pln"> xhr </span><span class="pun">=</span><span class="kwd">new</span><span class="typ">XMLHttpRequest</span><span class="pun">();</span><span class="pln">
xhr</span><span class="pun">.</span><span class="pln">open</span><span class="pun">(</span><span class="str">'GET'</span><span class="pun">,</span><span class="str">'/path/to/image.png'</span><span class="pun">,</span><span class="kwd">true</span><span class="pun">);</span>**<span class="pln">xhr</span><span class="pun">.</span><span class="pln">responseType </span><span class="pun">=</span><span class="str">'arraybuffer'</span><span class="pun">;</span>****<span class="pln">xhr</span><span class="pun">.</span><span class="pln">onload</span>**<span class="pun">=</span><span class="kwd">function</span><span class="pun">(</span><span class="pln">e</span><span class="pun">)</span><span class="pun">{</span><span class="kwd">var</span><span class="pln"> uInt8Array </span><span class="pun">=</span><span class="kwd">new</span>**<span class="typ">Uint8Array</span>**<span class="pun">(</span>**<span class="kwd">this</span><span class="pun">.</span><span class="pln">response</span>**<span class="pun">);</span><span class="com">// this.response == uInt8Array.buffer</span><span class="com">// var byte3 = uInt8Array[4]; // byte at offset 4</span><span class="pun">...</span><span class="pun">};</span><span class="pln">

xhr</span><span class="pun">.</span><span class="pln">send</span><span class="pun">();</span></pre>

#### Blob 响应

如果您要直接处理 [`Blob`](https://developer.mozilla.org/en/DOM/Blob) 且/或不需要操作任何文件的字节，可使用 `xhr.responseType='blob'`：

<pre class="prettyprint"><span class="pln">window</span><span class="pun">.</span><span class="pln">URL </span><span class="pun">=</span><span class="pln"> window</span><span class="pun">.</span><span class="pln">URL </span><span class="pun">||</span><span class="pln"> window</span><span class="pun">.</span><span class="pln">webkitURL</span><span class="pun">;</span><span class="com">// Take care of vendor prefixes.</span><span class="kwd">var</span><span class="pln"> xhr </span><span class="pun">=</span><span class="kwd">new</span><span class="typ">XMLHttpRequest</span><span class="pun">();</span><span class="pln">
xhr</span><span class="pun">.</span><span class="pln">open</span><span class="pun">(</span><span class="str">'GET'</span><span class="pun">,</span><span class="str">'/path/to/image.png'</span><span class="pun">,</span><span class="kwd">true</span><span class="pun">);</span>**<span class="pln">xhr</span><span class="pun">.</span><span class="pln">responseType </span><span class="pun">=</span><span class="str">'blob'</span><span class="pun">;</span>****<span class="pln">xhr</span><span class="pun">.</span><span class="pln">onload</span>**<span class="pun">=</span><span class="kwd">function</span><span class="pun">(</span><span class="pln">e</span><span class="pun">)</span><span class="pun">{</span><span class="kwd">if</span><span class="pun">(</span><span class="kwd">this</span><span class="pun">.</span><span class="pln">status </span><span class="pun">==</span><span class="lit">200</span><span class="pun">)</span><span class="pun">{</span><span class="kwd">var</span><span class="pln"> blob </span><span class="pun">=</span>**<span class="kwd">this</span><span class="pun">.</span><span class="pln">response</span>**<span class="pun">;</span><span class="kwd">var</span><span class="pln"> img </span><span class="pun">=</span><span class="pln"> document</span><span class="pun">.</span><span class="pln">createElement</span><span class="pun">(</span><span class="str">'img'</span><span class="pun">);</span><span class="pln">
    img</span><span class="pun">.</span><span class="pln">onload </span><span class="pun">=</span><span class="kwd">function</span><span class="pun">(</span><span class="pln">e</span><span class="pun">)</span><span class="pun">{</span>**<span class="pln">window</span><span class="pun">.</span><span class="pln">URL</span><span class="pun">.</span><span class="pln">revokeObjectURL</span><span class="pun">(</span><span class="pln">img</span><span class="pun">.</span><span class="pln">src</span><span class="pun">);</span>**<span class="com">// Clean up after yourself.</span><span class="pun">};</span><span class="pln">
    img</span><span class="pun">.</span><span class="pln">src </span><span class="pun">=</span>**<span class="pln">window</span><span class="pun">.</span><span class="pln">URL</span><span class="pun">.</span><span class="pln">createObjectURL</span><span class="pun">(</span><span class="pln">blob</span><span class="pun">);</span>**<span class="pln">
    document</span><span class="pun">.</span><span class="pln">body</span><span class="pun">.</span><span class="pln">appendChild</span><span class="pun">(</span><span class="pln">img</span><span class="pun">);</span><span class="pun">...</span><span class="pun">}</span><span class="pun">};</span><span class="pln">

xhr</span><span class="pun">.</span><span class="pln">send</span><span class="pun">();</span></pre>

`Blob` 可用于很多场合，包括保存到 [indexedDB](/tutorials/indexeddb/todo/)、写入 HTML5 [文件系统](/tutorials/file/filesystem/) 或[创建 Blob 网址](/tutorials/workers/basics/#toc-inlineworkers-bloburis)（如本例中所示）。

## 发送数据

能够[下载各种格式的数据](#toc-response)固然是件好事，但是如果不能将这些丰富格式的数据送回本垒（服务器），那就毫无意义了。`XMLHttpRequest` 有时候会限制我们发送 `DOMString` 或 `Document` (XML) 数据。但是现在不会了。现已替换成经过修改的 `send()` 方法，可接受以下任何类型：`DOMString`、`Document`、`FormData`、`Blob`、`File`、`ArrayBuffer`。本部分的其余内容中的示例演示了如何使用各类型发送数据。

### 发送字符串数据：xhr.send(DOMString)

<pre class="prettyprint" style="display: inline-block; margin-right: 10px; vertical-align: top;"><span class="kwd">function</span><span class="pln"> sendText</span><span class="pun">(</span><span class="pln">txt</span><span class="pun">)</span><span class="pun">{</span><span class="kwd">var</span><span class="pln"> xhr </span><span class="pun">=</span><span class="kwd">new</span><span class="typ">XMLHttpRequest</span><span class="pun">();</span><span class="pln">
  xhr</span><span class="pun">.</span><span class="pln">open</span><span class="pun">(</span><span class="str">'POST'</span><span class="pun">,</span><span class="str">'/server'</span><span class="pun">,</span><span class="kwd">true</span><span class="pun">);</span><span class="pln">
  xhr</span><span class="pun">.</span><span class="pln">onload </span><span class="pun">=</span><span class="kwd">function</span><span class="pun">(</span><span class="pln">e</span><span class="pun">)</span><span class="pun">{</span><span class="kwd">if</span><span class="pun">(</span><span class="kwd">this</span><span class="pun">.</span><span class="pln">status </span><span class="pun">==</span><span class="lit">200</span><span class="pun">)</span><span class="pun">{</span><span class="pln">
      console</span><span class="pun">.</span><span class="pln">log</span><span class="pun">(</span>**<span class="kwd">this</span><span class="pun">.</span><span class="pln">responseText</span>**<span class="pun">);</span><span class="pun">}</span><span class="pun">};</span><span class="pln">

  xhr</span><span class="pun">.</span><span class="pln">send</span><span class="pun">(</span><span class="pln">txt</span><span class="pun">);</span><span class="pun">}</span><span class="pln">

sendText</span><span class="pun">(</span>**<span class="str">'test string'</span>**<span class="pun">);</span></pre>
<pre class="prettyprint" style="display: inline-block;"><span class="kwd">function</span><span class="pln"> sendTextNew</span><span class="pun">(</span><span class="pln">txt</span><span class="pun">)</span><span class="pun">{</span><span class="kwd">var</span><span class="pln"> xhr </span><span class="pun">=</span><span class="kwd">new</span><span class="typ">XMLHttpRequest</span><span class="pun">();</span><span class="pln">
  xhr</span><span class="pun">.</span><span class="pln">open</span><span class="pun">(</span><span class="str">'POST'</span><span class="pun">,</span><span class="str">'/server'</span><span class="pun">,</span><span class="kwd">true</span><span class="pun">);</span>**<span class="pln">xhr</span><span class="pun">.</span><span class="pln">responseType </span><span class="pun">=</span><span class="str">'text'</span><span class="pun">;</span>**<span class="pln">
  xhr</span><span class="pun">.</span><span class="pln">onload </span><span class="pun">=</span><span class="kwd">function</span><span class="pun">(</span><span class="pln">e</span><span class="pun">)</span><span class="pun">{</span><span class="kwd">if</span><span class="pun">(</span><span class="kwd">this</span><span class="pun">.</span><span class="pln">status </span><span class="pun">==</span><span class="lit">200</span><span class="pun">)</span><span class="pun">{</span><span class="pln">
      console</span><span class="pun">.</span><span class="pln">log</span><span class="pun">(</span>**<span class="kwd">this</span><span class="pun">.</span><span class="pln">response</span>**<span class="pun">);</span><span class="pun">}</span><span class="pun">};</span><span class="pln">
  xhr</span><span class="pun">.</span><span class="pln">send</span><span class="pun">(</span><span class="pln">txt</span><span class="pun">);</span><span class="pun">}</span><span class="pln">

sendText2</span><span class="pun">(</span>**<span class="str">'test string'</span>**<span class="pun">);</span></pre>

这没有新内容，只是正确的代码段略有不同。其中设置了 `responseType='text'` 作为对比。再次说明，省略此行会得到同样的结果。

### 提交表单：xhr.send(FormData)

很多人可能习惯于使用 [jQuery 插件](http://jquery.malsup.com/form/)或其他库来处理 AJAX 表单提交。而我们可以改用 [`FormData`](https://developer.mozilla.org/en/XMLHttpRequest/FormData)，这是另一种针对 XHR2 设计的新数据类型。使用 `FormData` 能够很方便地实时以 JavaScript 创建 HTML `&lt;form&gt;`。然后可以使用 AJAX 提交该表单：

<pre class="prettyprint"><span class="kwd">function</span><span class="pln"> sendForm</span><span class="pun">()</span><span class="pun">{</span><span class="kwd">var</span><span class="pln"> formData </span><span class="pun">=</span><span class="kwd">new</span>**<span class="typ">FormData</span>**<span class="pun">();</span>**<span class="pln">formData</span><span class="pun">.</span><span class="pln">append</span>**<span class="pun">(</span><span class="str">'username'</span><span class="pun">,</span><span class="str">'johndoe'</span><span class="pun">);</span>**<span class="pln">formData</span><span class="pun">.</span><span class="pln">append</span>**<span class="pun">(</span><span class="str">'id'</span><span class="pun">,</span><span class="lit">123456</span><span class="pun">);</span><span class="kwd">var</span><span class="pln"> xhr </span><span class="pun">=</span><span class="kwd">new</span><span class="typ">XMLHttpRequest</span><span class="pun">();</span><span class="pln">
  xhr</span><span class="pun">.</span><span class="pln">open</span><span class="pun">(</span><span class="str">'POST'</span><span class="pun">,</span><span class="str">'/server'</span><span class="pun">,</span><span class="kwd">true</span><span class="pun">);</span><span class="pln">
  xhr</span><span class="pun">.</span><span class="pln">onload </span><span class="pun">=</span><span class="kwd">function</span><span class="pun">(</span><span class="pln">e</span><span class="pun">)</span><span class="pun">{</span><span class="pun">...</span><span class="pun">};</span><span class="pln">

  xhr</span><span class="pun">.</span><span class="pln">send</span><span class="pun">(</span>**<span class="pln">formData</span>**<span class="pun">);</span><span class="pun">}</span></pre>

实质上，我们只是动态创建了 `&lt;form&gt;`，并通过调用 append 方法为其附加了 `&lt;input&gt;` 值。

当然，您无需从一开始就创建 `&lt;form&gt;`。`FormData` 对象可通过页面上现有的 `HTMLFormElement` 进行初始化。例如：

<pre class="prettyprint"><span class="tag">&lt;form</span><span class="atn">id</span><span class="pun">=</span><span class="atv">"myform"</span><span class="atn">name</span><span class="pun">=</span><span class="atv">"myform"</span><span class="atn">action</span><span class="pun">=</span><span class="atv">"/server"</span><span class="tag">&gt;</span><span class="tag">&lt;input</span><span class="atn">type</span><span class="pun">=</span><span class="atv">"text"</span><span class="atn">name</span><span class="pun">=</span><span class="atv">"username"</span><span class="atn">value</span><span class="pun">=</span><span class="atv">"johndoe"</span><span class="tag">&gt;</span><span class="tag">&lt;input</span><span class="atn">type</span><span class="pun">=</span><span class="atv">"number"</span><span class="atn">name</span><span class="pun">=</span><span class="atv">"id"</span><span class="atn">value</span><span class="pun">=</span><span class="atv">"123456"</span><span class="tag">&gt;</span><span class="tag">&lt;input</span><span class="atn">type</span><span class="pun">=</span><span class="atv">"submit"</span><span class="atn">onclick</span><span class="pun">=</span><span class="atv">"</span>**<span class="kwd">return</span><span class="pln"> sendForm</span><span class="pun">(</span><span class="kwd">this</span><span class="pun">.</span><span class="pln">form</span><span class="pun">);</span>**<span class="atv">"</span><span class="tag">&gt;</span><span class="tag">&lt;/form&gt;</span></pre>
<pre class="prettyprint"><span class="kwd">function</span><span class="pln"> sendForm</span><span class="pun">(</span><span class="pln">form</span><span class="pun">)</span><span class="pun">{</span><span class="kwd">var</span><span class="pln"> formData </span><span class="pun">=</span><span class="kwd">new</span>**<span class="typ">FormData</span>**<span class="pun">(</span><span class="pln">form</span><span class="pun">);</span>**<span class="pln">formData</span><span class="pun">.</span><span class="pln">append</span>**<span class="pun">(</span><span class="str">'secret_token'</span><span class="pun">,</span><span class="str">'1234567890'</span><span class="pun">);</span><span class="com">// Append extra data before send.</span><span class="kwd">var</span><span class="pln"> xhr </span><span class="pun">=</span><span class="kwd">new</span><span class="typ">XMLHttpRequest</span><span class="pun">();</span><span class="pln">
  xhr</span><span class="pun">.</span><span class="pln">open</span><span class="pun">(</span><span class="str">'POST'</span><span class="pun">,</span><span class="pln"> form</span><span class="pun">.</span><span class="pln">action</span><span class="pun">,</span><span class="kwd">true</span><span class="pun">);</span><span class="pln">
  xhr</span><span class="pun">.</span><span class="pln">onload </span><span class="pun">=</span><span class="kwd">function</span><span class="pun">(</span><span class="pln">e</span><span class="pun">)</span><span class="pun">{</span><span class="pun">...</span><span class="pun">};</span><span class="pln">

  xhr</span><span class="pun">.</span><span class="pln">send</span><span class="pun">(</span>**<span class="pln">formData</span>**<span class="pun">);</span><span class="kwd">return</span><span class="kwd">false</span><span class="pun">;</span><span class="com">// Prevent page from submitting.</span><span class="pun">}</span></pre>

HTML 表单可包含文件上传（例如 `&lt;input type="file"&gt;`），而 `FormData` 也可以处理此操作。只需附加文件，浏览器就会在调用 `send()` 时构建 `multipart/form-data` 请求。

<pre class="prettyprint"><span class="kwd">function</span><span class="pln"> uploadFiles</span><span class="pun">(</span><span class="pln">url</span><span class="pun">,</span><span class="pln"> files</span><span class="pun">)</span><span class="pun">{</span><span class="kwd">var</span><span class="pln"> formData </span><span class="pun">=</span><span class="kwd">new</span>**<span class="typ">FormData</span>**<span class="pun">();</span><span class="kwd">for</span><span class="pun">(</span><span class="kwd">var</span><span class="pln"> i </span><span class="pun">=</span><span class="lit">0</span><span class="pun">,</span><span class="pln"> file</span><span class="pun">;</span><span class="pln"> file </span><span class="pun">=</span><span class="pln"> files</span><span class="pun">[</span><span class="pln">i</span><span class="pun">];</span><span class="pun">++</span><span class="pln">i</span><span class="pun">)</span><span class="pun">{</span>**<span class="pln">formData</span><span class="pun">.</span><span class="pln">append</span><span class="pun">(</span><span class="pln">file</span><span class="pun">.</span><span class="pln">name</span><span class="pun">,</span><span class="pln"> file</span><span class="pun">);</span>**<span class="pun">}</span><span class="kwd">var</span><span class="pln"> xhr </span><span class="pun">=</span><span class="kwd">new</span><span class="typ">XMLHttpRequest</span><span class="pun">();</span><span class="pln">
  xhr</span><span class="pun">.</span><span class="pln">open</span><span class="pun">(</span><span class="str">'POST'</span><span class="pun">,</span><span class="pln"> url</span><span class="pun">,</span><span class="kwd">true</span><span class="pun">);</span><span class="pln">
  xhr</span><span class="pun">.</span><span class="pln">onload </span><span class="pun">=</span><span class="kwd">function</span><span class="pun">(</span><span class="pln">e</span><span class="pun">)</span><span class="pun">{</span><span class="pun">...</span><span class="pun">};</span><span class="pln">

  xhr</span><span class="pun">.</span><span class="pln">send</span><span class="pun">(</span>**<span class="pln">formData</span>**<span class="pun">);</span><span class="com">// multipart/form-data</span><span class="pun">}</span><span class="pln">

document</span><span class="pun">.</span><span class="pln">querySelector</span><span class="pun">(</span><span class="str">'input[type="file"]'</span><span class="pun">).</span><span class="pln">addEventListener</span><span class="pun">(</span><span class="str">'change'</span><span class="pun">,</span><span class="kwd">function</span><span class="pun">(</span><span class="pln">e</span><span class="pun">)</span><span class="pun">{</span><span class="pln">
  uploadFiles</span><span class="pun">(</span><span class="str">'/server'</span><span class="pun">,</span><span class="kwd">this</span><span class="pun">.</span><span class="pln">files</span><span class="pun">);</span><span class="pun">},</span><span class="kwd">false</span><span class="pun">);</span></pre>

### 上传文件或 blob：xhr.send(Blob)

我们也可以使用 XHR 发送 `File` 或 `Blob`。请注意，所有 `File` 都是 `Blob`，所以在此使用两者皆可。

该示例使用 `BlobBuilder` API 从头开始创建新的文本文件，并将该 `Blob` 上传到服务器。该代码还设置了一个处理程序，用于通知用户上传进度：

<pre class="prettyprint"><span class="tag">&lt;progress</span><span class="atn">min</span><span class="pun">=</span><span class="atv">"0"</span><span class="atn">max</span><span class="pun">=</span><span class="atv">"100"</span><span class="atn">value</span><span class="pun">=</span><span class="atv">"0"</span><span class="tag">&gt;</span><span class="pln">0% complete</span><span class="tag">&lt;/progress&gt;</span></pre>
<pre class="prettyprint"><span class="kwd">function</span><span class="pln"> upload</span><span class="pun">(</span><span class="pln">blobOrFile</span><span class="pun">)</span><span class="pun">{</span><span class="kwd">var</span><span class="pln"> xhr </span><span class="pun">=</span><span class="kwd">new</span><span class="typ">XMLHttpRequest</span><span class="pun">();</span><span class="pln">
  xhr</span><span class="pun">.</span><span class="pln">open</span><span class="pun">(</span><span class="str">'POST'</span><span class="pun">,</span><span class="str">'/server'</span><span class="pun">,</span><span class="kwd">true</span><span class="pun">);</span><span class="pln">
  xhr</span><span class="pun">.</span><span class="pln">onload </span><span class="pun">=</span><span class="kwd">function</span><span class="pun">(</span><span class="pln">e</span><span class="pun">)</span><span class="pun">{</span><span class="pun">...</span><span class="pun">};</span><span class="com">// Listen to the upload progress.</span><span class="kwd">var</span><span class="pln"> progressBar </span><span class="pun">=</span><span class="pln"> document</span><span class="pun">.</span><span class="pln">querySelector</span><span class="pun">(</span><span class="str">'progress'</span><span class="pun">);</span>**<span class="pln">xhr</span><span class="pun">.</span><span class="pln">upload</span><span class="pun">.</span><span class="pln">onprogress</span>**<span class="pun">=</span><span class="kwd">function</span><span class="pun">(</span><span class="pln">e</span><span class="pun">)</span><span class="pun">{</span><span class="kwd">if</span><span class="pun">(</span><span class="pln">e</span><span class="pun">.</span><span class="pln">lengthComputable</span><span class="pun">)</span><span class="pun">{</span><span class="pln">
      progressBar</span><span class="pun">.</span><span class="pln">value </span><span class="pun">=</span><span class="pun">(</span><span class="pln">e</span><span class="pun">.</span><span class="pln">loaded </span><span class="pun">/</span><span class="pln"> e</span><span class="pun">.</span><span class="pln">total</span><span class="pun">)</span><span class="pun">*</span><span class="lit">100</span><span class="pun">;</span><span class="pln">
      progressBar</span><span class="pun">.</span><span class="pln">textContent </span><span class="pun">=</span><span class="pln"> progressBar</span><span class="pun">.</span><span class="pln">value</span><span class="pun">;</span><span class="com">// Fallback for unsupported browsers.</span><span class="pun">}</span><span class="pun">};</span><span class="pln">

  xhr</span><span class="pun">.</span><span class="pln">send</span><span class="pun">(</span>**<span class="pln">blobOrFile</span>**<span class="pun">);</span><span class="pun">}</span><span class="com">// Take care of vendor prefixes.</span><span class="typ">BlobBuilder</span><span class="pun">=</span><span class="pln"> window</span><span class="pun">.</span><span class="typ">MozBlobBuilder</span><span class="pun">||</span><span class="pln"> window</span><span class="pun">.</span><span class="typ">WebKitBlobBuilder</span><span class="pun">||</span><span class="pln"> window</span><span class="pun">.</span><span class="typ">BlobBuilder</span><span class="pun">;</span><span class="kwd">var</span><span class="pln"> bb </span><span class="pun">=</span><span class="kwd">new</span>**<span class="typ">BlobBuilder</span>**<span class="pun">();</span>**<span class="pln">bb</span><span class="pun">.</span><span class="pln">append</span>**<span class="pun">(</span><span class="str">'hello world'</span><span class="pun">);</span><span class="pln">

upload</span><span class="pun">(</span>**<span class="pln">bb</span><span class="pun">.</span><span class="pln">getBlob</span><span class="pun">(</span><span class="str">'text/plain'</span><span class="pun">)</span>**<span class="pun">);</span></pre>

### 上传字节：xhr.send(ArrayBuffer)

最后也是相当重要的一点就是，我们能以 XHR 的有效负载形式发送 `ArrayBuffer`。

<pre class="prettyprint"><span class="kwd">function</span><span class="pln"> sendArrayBuffer</span><span class="pun">()</span><span class="pun">{</span><span class="kwd">var</span><span class="pln"> xhr </span><span class="pun">=</span><span class="kwd">new</span><span class="typ">XMLHttpRequest</span><span class="pun">();</span><span class="pln">
  xhr</span><span class="pun">.</span><span class="pln">open</span><span class="pun">(</span><span class="str">'POST'</span><span class="pun">,</span><span class="str">'/server'</span><span class="pun">,</span><span class="kwd">true</span><span class="pun">);</span><span class="pln">
  xhr</span><span class="pun">.</span><span class="pln">onload </span><span class="pun">=</span><span class="kwd">function</span><span class="pun">(</span><span class="pln">e</span><span class="pun">)</span><span class="pun">{</span><span class="pun">...</span><span class="pun">};</span>**<span class="kwd">var</span><span class="pln"> uInt8Array </span><span class="pun">=</span><span class="kwd">new</span>****<span class="typ">Uint8Array</span>**<span class="pun">([</span><span class="lit">1</span><span class="pun">,</span><span class="lit">2</span><span class="pun">,</span><span class="lit">3</span><span class="pun">]);</span><span class="pln">

  xhr</span><span class="pun">.</span><span class="pln">send</span><span class="pun">(</span>**<span class="pln">uInt8Array</span><span class="pun">.</span><span class="pln">buffer</span>**<span class="pun">);</span><span class="pun">}</span></pre>

## 跨源资源共享 (CORS)

[CORS](http://dev.w3.org/2006/waf/access-control/) 允许一个域上的网络应用向另一个域提交跨域 AJAX 请求。启用此功能非常简单，只需由服务器发送一个响应标头即可。

### 启用 CORS 请求

假设您的应用已经在 `example.com` 上了，而您想要从 `www.example2.com` 提取数据。一般情况下，如果您尝试进行这种类型的 AJAX 调用，请求将会失败，而浏览器将会出现“源不匹配”的错误。利用 CORS，`www.example2.com` 只需添加一个标头，就可以允许来自 `example.com` 的请求：

<pre class="prettyprint"><span class="typ">Access</span><span class="pun">-</span><span class="typ">Control</span><span class="pun">-</span><span class="typ">Allow</span><span class="pun">-</span><span class="typ">Origin</span><span class="pun">:</span><span class="pln"> http</span><span class="pun">:</span><span class="com">//example.com</span></pre>

可将 `Access-Control-Allow-Origin` 添加到某网站下或整个域中的单个资源。要允许任何域向您提交请求，请设置如下：

<pre class="prettyprint"><span class="typ">Access</span><span class="pun">-</span><span class="typ">Control</span><span class="pun">-</span><span class="typ">Allow</span><span class="pun">-</span><span class="typ">Origin</span><span class="pun">:</span>**<span class="pun">*</span>**
</pre>

其实，该网站 (html5rocks.com) 已在其所有网页上均启用了 CORS。启用开发人员工具后，您就会在我们的响应中看到 `Access-Control-Allow-Origin` 了：

<figure>![Access-Control-Allow-Origin header on html5rocks.com](access_control_header.png "html5rocks.com 上的 Access-Control-Allow-Origin 标头")

<figcaption>html5rocks.com 上的 `Access-Control-Allow-Origin` 标头</figcaption></figure>

启用跨源请求是非常简单的，因此如果您的数据是公开的，请务必[启用 CORS](http://enable-cors.org/)！

### 提交跨域请求

如果服务器端已启用了 CORS，那么提交跨域请求就和普通的 `XMLHttpRequest` 请求没什么区别。例如，现在 `example.com` 可以向 `www.example2.com` 提交请求了：

<pre class="prettyprint"><span class="kwd">var</span><span class="pln"> xhr </span><span class="pun">=</span><span class="kwd">new</span><span class="typ">XMLHttpRequest</span><span class="pun">();</span><span class="pln">
xhr</span><span class="pun">.</span><span class="pln">open</span><span class="pun">(</span><span class="str">'GET'</span><span class="pun">,</span><span class="str">'http://www.example2.com/hello.json'</span><span class="pun">);</span><span class="pln">
xhr</span><span class="pun">.</span><span class="pln">onload </span><span class="pun">=</span><span class="kwd">function</span><span class="pun">(</span><span class="pln">e</span><span class="pun">)</span><span class="pun">{</span><span class="kwd">var</span><span class="pln"> data </span><span class="pun">=</span><span class="pln"> JSON</span><span class="pun">.</span><span class="pln">parse</span><span class="pun">(</span>**<span class="kwd">this</span><span class="pun">.</span><span class="pln">response</span>**<span class="pun">);</span><span class="pun">...</span><span class="pun">}</span><span class="pln">
xhr</span><span class="pun">.</span><span class="pln">send</span><span class="pun">();</span></pre>

## 实际示例：

### 下载文件并保存到 HTML5 文件系统

假设您有一个图片库，想要提取一些图片，然后使用 [HTML5 文件系统](/tutorials/file/filesystem/)本地保存这些图片。一种方法是以 `ArrayBuffer` 形式请求图片，通过数据构建 `Blob`，并使用 `FileWriter` 写入 blob：

<pre class="prettyprint"><span class="pln">window</span><span class="pun">.</span><span class="pln">requestFileSystem  </span><span class="pun">=</span><span class="pln"> window</span><span class="pun">.</span><span class="pln">requestFileSystem </span><span class="pun">||</span><span class="pln"> window</span><span class="pun">.</span><span class="pln">webkitRequestFileSystem</span><span class="pun">;</span><span class="kwd">function</span><span class="pln"> onError</span><span class="pun">(</span><span class="pln">e</span><span class="pun">)</span><span class="pun">{</span><span class="pln">
  console</span><span class="pun">.</span><span class="pln">log</span><span class="pun">(</span><span class="str">'Error'</span><span class="pun">,</span><span class="pln"> e</span><span class="pun">);</span><span class="pun">}</span><span class="kwd">var</span><span class="pln"> xhr </span><span class="pun">=</span><span class="kwd">new</span><span class="typ">XMLHttpRequest</span><span class="pun">();</span><span class="pln">
xhr</span><span class="pun">.</span><span class="pln">open</span><span class="pun">(</span><span class="str">'GET'</span><span class="pun">,</span><span class="str">'/path/to/image.png'</span><span class="pun">,</span><span class="kwd">true</span><span class="pun">);</span>**<span class="pln">xhr</span><span class="pun">.</span><span class="pln">responseType </span><span class="pun">=</span><span class="str">'arraybuffer'</span><span class="pun">;</span>****<span class="pln">xhr</span><span class="pun">.</span><span class="pln">onload</span>**<span class="pun">=</span><span class="kwd">function</span><span class="pun">(</span><span class="pln">e</span><span class="pun">)</span><span class="pun">{</span>**<span class="pln">window</span><span class="pun">.</span><span class="pln">requestFileSystem</span>**<span class="pun">(</span><span class="pln">TEMPORARY</span><span class="pun">,</span><span class="lit">1024</span><span class="pun">*</span><span class="lit">1024</span><span class="pun">,</span><span class="kwd">function</span><span class="pun">(</span><span class="pln">fs</span><span class="pun">)</span><span class="pun">{</span><span class="pln">
    fs</span><span class="pun">.</span><span class="pln">root</span><span class="pun">.</span><span class="pln">getFile</span><span class="pun">(</span><span class="str">'image.png'</span><span class="pun">,</span><span class="pun">{</span><span class="pln">create</span><span class="pun">:</span><span class="kwd">true</span><span class="pun">},</span><span class="kwd">function</span><span class="pun">(</span><span class="pln">fileEntry</span><span class="pun">)</span><span class="pun">{</span><span class="pln">
      fileEntry</span><span class="pun">.</span><span class="pln">createWriter</span><span class="pun">(</span><span class="kwd">function</span><span class="pun">(</span><span class="pln">writer</span><span class="pun">)</span><span class="pun">{</span><span class="pln">

        writer</span><span class="pun">.</span><span class="pln">onwrite </span><span class="pun">=</span><span class="kwd">function</span><span class="pun">(</span><span class="pln">e</span><span class="pun">)</span><span class="pun">{</span><span class="pun">...</span><span class="pun">};</span><span class="pln">
        writer</span><span class="pun">.</span><span class="pln">onerror </span><span class="pun">=</span><span class="kwd">function</span><span class="pun">(</span><span class="pln">e</span><span class="pun">)</span><span class="pun">{</span><span class="pun">...</span><span class="pun">};</span>**<span class="kwd">var</span><span class="pln"> bb </span><span class="pun">=</span><span class="kwd">new</span><span class="typ">BlobBuilder</span><span class="pun">();</span>****<span class="pln">bb</span><span class="pun">.</span><span class="pln">append</span>**<span class="pun">(</span>**<span class="pln">xhr</span><span class="pun">.</span><span class="pln">response</span>**<span class="pun">);</span>**<span class="pln">writer</span><span class="pun">.</span><span class="pln">write</span>**<span class="pun">(</span><span class="pln">bb</span><span class="pun">.</span><span class="pln">getBlob</span><span class="pun">(</span><span class="str">'image/png'</span><span class="pun">));</span><span class="pun">},</span><span class="pln"> onError</span><span class="pun">);</span><span class="pun">},</span><span class="pln"> onError</span><span class="pun">);</span><span class="pun">},</span><span class="pln"> onError</span><span class="pun">);</span><span class="pun">};</span><span class="pln">

xhr</span><span class="pun">.</span><span class="pln">send</span><span class="pun">();</span></pre>

**请注意**：要使用此代码，请参阅“[探索 FileSystem API](/tutorials/file/filesystem/)”教程中的[浏览器支持和存储限制](/tutorials/file/filesystem/#toc-support)。

### 分割文件并上传各个部分

使用 [File API](/tutorials/file/dndfiles/)，我们可以将操作简化为上传大文件。我们采用的技术是：将要上传的文件分割成多个部分，为每个部分生成一个 XHR，然后在服务器上将各部分组合成文件。这类似于 Gmail 快速上传大附件的方法。使用这种技术还可以规避 Google 应用引擎对 http 请求的 32 MB 限制。

<pre class="prettyprint"><span class="pln">window</span><span class="pun">.</span><span class="typ">BlobBuilder</span><span class="pun">=</span><span class="pln"> window</span><span class="pun">.</span><span class="typ">MozBlobBuilder</span><span class="pun">||</span><span class="pln"> window</span><span class="pun">.</span><span class="typ">WebKitBlobBuilder</span><span class="pun">||</span><span class="pln">
                     window</span><span class="pun">.</span><span class="typ">BlobBuilder</span><span class="pun">;</span><span class="kwd">function</span><span class="pln"> upload</span><span class="pun">(</span><span class="pln">blobOrFile</span><span class="pun">)</span><span class="pun">{</span><span class="kwd">var</span><span class="pln"> xhr </span><span class="pun">=</span><span class="kwd">new</span><span class="typ">XMLHttpRequest</span><span class="pun">();</span><span class="pln">
  xhr</span><span class="pun">.</span><span class="pln">open</span><span class="pun">(</span><span class="str">'POST'</span><span class="pun">,</span><span class="str">'/server'</span><span class="pun">,</span><span class="kwd">true</span><span class="pun">);</span><span class="pln">
  xhr</span><span class="pun">.</span><span class="pln">onload </span><span class="pun">=</span><span class="kwd">function</span><span class="pun">(</span><span class="pln">e</span><span class="pun">)</span><span class="pun">{</span><span class="pun">...</span><span class="pun">};</span><span class="pln">
  xhr</span><span class="pun">.</span><span class="pln">send</span><span class="pun">(</span><span class="pln">blobOrFile</span><span class="pun">);</span><span class="pun">}</span><span class="pln">

document</span><span class="pun">.</span><span class="pln">querySelector</span><span class="pun">(</span><span class="str">'input[type="file"]'</span><span class="pun">).</span><span class="pln">addEventListener</span><span class="pun">(</span><span class="str">'change'</span><span class="pun">,</span><span class="kwd">function</span><span class="pun">(</span><span class="pln">e</span><span class="pun">)</span><span class="pun">{</span><span class="kwd">var</span><span class="pln"> blob </span><span class="pun">=</span><span class="kwd">this</span><span class="pun">.</span><span class="pln">files</span><span class="pun">[</span><span class="lit">0</span><span class="pun">];</span><span class="kwd">const</span><span class="pln"> BYTES_PER_CHUNK </span><span class="pun">=</span><span class="lit">1024</span><span class="pun">*</span><span class="lit">1024</span><span class="pun">;</span><span class="com">// 1MB chunk sizes.</span><span class="kwd">const</span><span class="pln"> SIZE </span><span class="pun">=</span><span class="pln"> blob</span><span class="pun">.</span><span class="pln">size</span><span class="pun">;</span><span class="kwd">var</span><span class="pln"> start </span><span class="pun">=</span><span class="lit">0</span><span class="pun">;</span><span class="kwd">var</span><span class="kwd">end</span><span class="pun">=</span><span class="pln"> BYTES_PER_CHUNK</span><span class="pun">;</span><span class="kwd">while</span><span class="pun">(</span><span class="pln">start </span><span class="pun">&lt;</span><span class="pln"> SIZE</span><span class="pun">)</span><span class="pun">{</span><span class="com">// Note: blob.slice has changed semantics and been prefixed. See http://goo.gl/U9mE5.</span><span class="kwd">if</span><span class="pun">(</span><span class="str">'mozSlice'</span><span class="kwd">in</span><span class="pln"> blob</span><span class="pun">)</span><span class="pun">{</span><span class="kwd">var</span><span class="pln"> chunk </span><span class="pun">=</span><span class="pln"> blob</span><span class="pun">.</span><span class="pln">mozSlice</span><span class="pun">(</span><span class="pln">start</span><span class="pun">,</span><span class="kwd">end</span><span class="pun">);</span><span class="pun">}</span><span class="kwd">else</span><span class="pun">{</span><span class="kwd">var</span><span class="pln"> chunk </span><span class="pun">=</span><span class="pln"> blob</span><span class="pun">.</span><span class="pln">webkitSlice</span><span class="pun">(</span><span class="pln">start</span><span class="pun">,</span><span class="kwd">end</span><span class="pun">);</span><span class="pun">}</span><span class="pln">

    upload</span><span class="pun">(</span><span class="pln">chunk</span><span class="pun">);</span><span class="pln">

    start </span><span class="pun">=</span><span class="kwd">end</span><span class="pun">;</span><span class="kwd">end</span><span class="pun">=</span><span class="pln"> start </span><span class="pun">+</span><span class="pln"> BYTES_PER_CHUNK</span><span class="pun">;</span><span class="pun">}</span><span class="pun">},</span><span class="kwd">false</span><span class="pun">);</span><span class="pun">})();</span></pre>

用于在服务器上重组文件的代码并未在此显示。

**赶快试试吧**！

<div class="example">#bytes/chunk: <input id="numChunks" min="1048576" type="number" value="1048576" />

<input id="afile" class="button" type="file" /></p>
<div id="bars"></div>
</div>

## 参考

*   [2 级 XMLHttpRequest](http://dev.w3.org/2006/webapi/XMLHttpRequest-2/) 规范
*   [跨源资源共享 (CORS)](http://dev.w3.org/2006/waf/access-control/) 规范
*   [File API](http://www.w3.org/TR/file-upload/) 规范
*   [FileSystem API](http://dev.w3.org/2009/dap/file-system/pub/FileSystem/) 规范

<!--more-->

<!--more-->

<!--more-->