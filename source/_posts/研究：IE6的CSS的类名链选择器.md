title: 研究：IE6的CSS的类名链选择器
tags:
  - "html&css"
  - ie6 bug
  - multiple classes selector
  - 多类名选择器
date: 2010-11-26 16:11:31
---

为了实现js状态，最近偶会这样去写css选择器：

.a ,  .b {color:red}

.a.c {color:green}

.b.c {color:#f60}

js可以方便的给.a, .b两个元素添加状态类.c，以改变其表现。但是在IE6下是挫败的。

![](http://www.cssor.com/wp-content/uploads/2010/11/chain-class.png "Firefox与ie6效果对比")

中文的标准说法是这样的：<span style="color: #ff0000;">CSS的类名交集复合选择器</span>：由两个选择器直接连接构成，其结果是选中二者各自元素范围的交集，其中<span style="color: #ff0000;">第一必须是标记选择器</span>，第二个必须是类别选择器或者ID选择器，两个选择器之间不能有空格，必须连续书写

如：h3.class{color:red;font-size:23px;}

另外就是搜了下老外的研究，貌似这个网站很不错：

http://www.maxdesign.com.au/articles/multiple-classes/

> Most browsers, excluding Win/IE5 and Win/IE6 support multiple class selectors.
> 
> Browser support chartMac: Safari 4.0	supported
> 
> Mac: Chrome 5.0	supported
> 
> Mac: FireFox 3.6	supported
> 
> Mac: Opera 10	supported
> 
> Win: FireFox 3.6	supported
> 
> Win: Opera 10	supported
> 
> Win: IE6	NOT SUPPORTED
> 
> Win: IE7	supported
> 
> Win: IE8	supported