---
title: vuejs与后台数据的交互实践
date: 2016-07-17 15:58:17
tags: vuejs
categories: 前端
---

Vuejs是MVVM框架，数据和DOM视图双向绑定，数据驱动的 web 界面的库。

**响应的数据绑定**：数据与DOM保持同步非常简单，数据发生变化的时候，如果我们使用JQuery操作DOM的话自然是可行的，但是jQuery操作DOM代码复杂容易出错，就拿一个简单的例子，在列表中新增一行数据，使用JQuery可能会刷新整个DOM,并对页面refresh。但是vue实现数据与DOM的绑定后，只要数据发生变化，就自动改动DOM，所以我们的工作重点就是修改数据，不必与DOM更新搅在一起。

**我的理解**：Vuejs简化了操作DOM或者不再去手动操作DOM，一个简单的API就搞定了DOM的自动更新，让我们脱离了使用Js操作DOM的复杂流程。这也是Vue的迷人之处吧，其他的优点诸如：组件化、模块化、体积小等优点我就不再罗列了，本文重点也不在此。
<!-- more -->
那么问题来了
## vuejs与后端（比如PHP）是如何交互的？

怎么使用Vuejs与后台数据（这里是指后台的数据库，不是vuejs的model，因为我理解的vuejs的model仍是前端数据）交互？其实这个问题本身就存在问题，（这是最初是对vuejs不怎么了解的情况下做出的提问），事实上vuejs **关注点**是如何优雅的操作DOM或者减少手动操作DOM，至于与后台的交互，这是js（当然vuejs也是基于js啊）要做的事情，所以**正确的提问姿势**应该是：*js怎样和后端交互？* 那么现在这个问题就变得简单了。我们知道js是不能直接操作数据库的，但是ajax却架起前后端的桥梁，能让前后端数据进行交互。

> 当然vuejs作为js衍生出的框架，它除了具有js的特性外，还有自己的特色呢，所以vuejs可以使用vue-resource来与后端交互。

下面使用ajax(基于jquery)、vuejs、php实现简单的数据的增删改查。

```
var url = 'http://localhost:8080/vuejs/api/book.php';
getJson(url, pushDom);
```

分析getJson函数【已经封装】
1. 初始化信息列表
2. 两个参数：
（1）第一个是获取列表的接口地址url；
（2）第二个是回调函数名
3. 流程分析：
在getJson中通过ajax调取接口url获取数据data，然后将data作为参数调用pushDom函数，pushDom是展示、或者使用vue操作DOM的功能函数。那么怎么使用jquey、ajax配合vue进行后端数据库增删改查。
首先需要对vue的增删改查机制了如指掌才行；methods事件触发，我们可以在methods中定义add、delete、modify完成数据的绑定，异步刷新显示；同时将获取的数据传递给封装好的ajax函数中，与后端交互【正确的流程应该与后台交互成功才会自动更新DOM，这里不细论】；例如
**添加数据**：
```
methods:{
    addBook:function(){
        this.book.id=parseInt(postData(this.book));
        this.books.push(this.book);
        this.book='';
    }
}
```
我们从前端拿到的数据`this.book`，然后调用`postData(this.book)`;`postData`就是一个与后端交互的封装了ajax的函数
期间遇到了一点小意外，总是获取不到ajax的返回值，关于如何获取ajax返回值和为何获取不到ajax中的值问题，`postData`函数中写的很清楚
`async: false`, 默认是`true`

代码见：[github][1]
demo页：![demo][2]

  [1]: https://github.com/upeng/learn-vuejs
  [2]: https://raw.githubusercontent.com/upeng/upeng.github.io/master/image/vuedemo.png