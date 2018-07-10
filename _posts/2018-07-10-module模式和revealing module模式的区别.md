---
layout: post
title: module模式和revealing module（揭示模块）模式的区别
tags:
  - 设计模式
  - module pattern
---

最近在看《JavaScript设计模式》，里面的module模式和revealing module模式区别非常有意思

### module模式

通过module模式结合闭包可以在JS中实现私有变量和私有方法


```javascript
var myNamespace = (function () {
  var myPrivateVar = 0
  var myPrivateMethod = function (foo) {
    console.log(foo)
  }
  return {
    myPublicVar: 'foo',
    myPublicMethod: function (bar) {
      myPrivateVar++
      myPrivateMethod(bar)
    }
  }
})();
```
如果将上面代码修改成揭示模块模式就是下面这样

### revealing module模式

```javascript
var myNamespace = (function () {
  var myPrivateVar = 0,
      myPublicVar = 'foo'
  var myPrivateMethod = function (foo) {
    console.log(foo)
  }
  var myPublicMethod = function (bar) {
    myPrivateVar++
    myPrivateMethod(bar)
  }
  return {
    publicVar: myPublicVar,
    publicMethod: myPublicMethod
  }
})();
```
两种写法带来了一些微妙的差异，而之所以出现揭示模块模式（即后者）是因为，模式的作者发现从一个方法调用公共方法必须要重复主对象的名称，就像下面这样

```javascript
var myNamespace = (function () {
  var myPrivateVar = 0
  var myPrivateMethod = function (foo) {
    console.log(foo)
  }
  var testPublicVar = function () {
    console.log(myNamespace.myPublicVar)
  }
  return {
    myPublicVar: 'foo',
    myPublicMethod: function (bar) {
      myPrivateVar++
      myPrivateMethod(bar)
    },
    test: function () {
      testPublicVar()
    }
  }
})();
```
在testPublicVar方法中，要想调用公共变量或者方法，必须使用myNamespace间接调用，不然就会报undefined错误，因为testPublicVar方法无法访问到最后返回的对象内部

对比之下，revealing module模式就解决了这个问题，而且用指针去声明公共变量和方法可读性稍好一些且可以灵活指定别名

但是揭示模块模式依然有它的缺点，根据书中的描述

> 该模式的一个缺点：如果一个私有函数引用一个公有函数，在需要打补丁时，公有函数是不能被覆盖的。这是因为私有函数将继续引用私有实现，该模式并不适用于公有成员，只适用于函数。
> 引用私有变量的公有对象成员也遵循无补丁规则。
> 正因如此，采用Revealing Module模式创建的模块可能比那些采用原始Module模式创建的模块更加脆弱，所以在使用时应该特别小心。

这一部分并没有看懂，先记下来方便以后补充