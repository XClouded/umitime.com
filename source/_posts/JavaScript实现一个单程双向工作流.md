title: JavaScript实现一个单程双向工作流
tags:
  - JavaScript
  - solution
  - workflow
  - 工作流
date: 2014-08-25 19:19:07
---

初衷源于项目中上一步，下一步这种交互需求，而每一步有可能或全部是异步操作，而且会有数据临时存储与获取的需要。

考虑了一些方案，受requirejs，seajs实现的启发，最终实现了这个单程双向工作流对象，而其源码看似相当简单。

那么怎么使用呢？当var flow = new Flow()实例化一个工作流以后，可以调用如下API实现：

1、流程添加API 3个：

<pre>
// 参数action是一个function回调，比如
// function(i, data) {}
// i是当前步骤的索引，从0开始
// data是所有步骤存储的数据数组，因为本工作流提供数据存储，也自然提供获取
// data[i]即 第i步的数据
// action中的this指向为flow实例对象
flow.start(action); // start其实内部实现为调用next方法，因此start等同于next，其存在价值大概是为了语义
flow.next(action);  // next，添加下一步action
flow.done(action);  // done，完成所有步骤，该方法也是启动整个工作流的必须方法，也必须是最后调用。
</pre>

<span id="more-1203"></span>

2、流程控制API 3个：

<pre>
// 如下API应该在流程添加的action中的条件控制中调用
// waiting()设计为一种标识作用，本身只是个空函数，当action中有prevStep, nextStep的方法调用时，该方法就不需要调用。
flow.prevStep(); // 在action中，完成某些任务之后调用该api将执行下一步action
flow.nextStep(); // 在action中，完成某些任务之后调用该api将返回执行上一步action
flow.waiting();  // 流程等待，停留在当前步骤中。
</pre>

3、数据存储API 1个：

<pre>
// data可选，在某一步action中，调用store, 传了data，将存储data，不传则为获取该步骤的data
// 在action中，如果有return语句，则return的值将自动被存储
// 例如 flow.next(function(i, data){
//    return {a: 1}; // 自动被存储为 data[i]
// });
flow.store(data)
</pre>

### 一个详细的数据存储的例子

<pre>
flow.next(function(i, data){
    // 以下获取数据方式是等价的
    var a = data[i];
    var b = this.store();

    // 以下存储数据方式是等价的
    this.store('hello store!');
    return 'hello store';
}).done(function(i, data){
    console.info(data[0]);// 输出第一步的数据： hello store
});
</pre>

### 一个完整的流程模拟的例子：

<button id="run-flow-js" style="padding: 3px 10px">点击运行</button>

<div id="run-flow-js-result"></div>
<pre>
var flow = new Flow(), j = 0;
flow.start(function() {
    j++;
    console.info('j : ' + j);
}).next(function() {
    if (j > 2) {
        console.info('j = ' + j, ' 2秒后继续下一步')
        setTimeout(function(){
          flow.nextStep();
        }, 2000)
    } else {
        console.info('j = ' + j, ' 返回上一步')
        // 异步情况下：
        // setTimeout(function(){
        //   flow.prevStep();
        // }, 2000)
        this.prevStep();
    }
}).next(function () {
    console.info('j理应为3，实际 j = ' , j);
}).done();
// 输出结果：
// j : 1
// j = 1  返回上一步
// j : 2
// j = 2  返回上一步
// j : 3
// j = 3  2秒后继续下一步
// j理应为3，实际 j =  3
</pre>

**PS. **

1、受js和实现原理的限制，调用prevStep的action中也必须在某个条件下调用nextStep，否则会发生死循环。而单独调用nextStep不受此限制。

2、流程添加API支持链式调用，例如： flow.start().next().next().done();

源码： https://github.com/tofishes/app/blob/master/flow.js