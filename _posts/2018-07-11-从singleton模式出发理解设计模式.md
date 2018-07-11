---
layout: default
title: 从singleton模式出发理解设计模式
tags: 
 - 设计模式
 - singleton pattern
---
首先，我们直接来看书中提供的代码实现

```javascript
var mySingleton = (function () {

  // Instance stores a reference to the Singleton
  var instance;

  function init() {

    // Singleton

    // Private methods and variables
    function privateMethod(){
        console.log( "I am private" );
    }

    var privateVariable = "Im also private";

    var privateRandomNumber = Math.random();

    return {

      // Public methods and variables
      publicMethod: function () {
        console.log( "The public can see me!" );
      },

      publicProperty: "I am also public",

      getRandomNumber: function() {
        return privateRandomNumber;
      }

    };

  };

  return {

    // Get the Singleton instance if one exists
    // or create one if it doesn't
    getInstance: function () {

      if ( !instance ) {
        instance = init();
      }

      return instance;
    }

  };

})();
```
以前，第一次看到单例模式，直觉觉得这应该是最简单的模式了吧，毕竟带着个“单”字么……但是在众多模式中，作者并没有把单例模式放在第一的位置，而是放在了构造器模式和模块模式的后面，作为第三个介绍的设计模式

看了上面实现的代码我们也可以感觉到，模式之间并不是毫无关系的，模式之间存在着看似错综复杂的“依赖”关系，而构造器模式和模块模式就是其他模式的基础

进一步分析，这个singleton实现中，首先是使用了**匿名函数**，匿名函数中的子函数init就是使用**模块模式**返回了一个实例对象，同级的成员对象instance指向这个新建的实例，最终，匿名函数返回一个拥有getInstance方法的对象，这个方法会判断当前上下文中是否存在instance，如果不存在就调用init方法生成一个，如果存在，就返回已经存在的instance，从而**保证instance只会被生成一次**——这个结果即singleton pattern所要实现的效果

例子中的实例对象是使用**对象直接量**生成的，当然也可以换用**构造器**去生成