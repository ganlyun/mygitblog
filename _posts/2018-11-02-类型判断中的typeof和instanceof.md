---
layout: post
title: 类型判断中的typeof和instanceof
author: ganlyun
tags:
 - 类型判断
 - instanceof
 - typeof
---

# 1. typeof

我们可以利用 `typeof` 来判断`number`, `string`,`object`, `boolean`, `function`, `undefined`, `symbol`，缺点是不能详细判断哪一种object，只能继续使用instanceof判断

> typeof [] 会返回 object，数组可以使用 Array.isArray([]) 来进行判断
> typeof null 会返回 object

### 原理

js 在底层存储变量的时候，会在变量的机器码的低位1-3位存储其类型信息👉

*   000：对象
*   010：浮点数
*   100：字符串
*   110：布尔
*   1：整数

但是对于 `undefined` 和 `null` 来说，这两个值的信息存储是有点特殊的。

`null`：所有机器码均为0

`undefined`：用 −2^30 整数来表示

所以，`typeof` 在判断 `null` 的时候就出现问题了，由于 `null` 的所有机器码均为0，因此直接被当做了对象来看待。

```js
null instanceof null // TypeError: Right-hand side of 'instanceof' is not an object
```

# 2. instanceof

```js
let person = function () { } 
let nicole = new person() 
nicole instanceof person // true
```

```js
let person = function () { } 
let programmer = function () { } 
programmer.prototype = new person() 
let nicole = new programmer() 
nicole instanceof person // true 
nicole instanceof programmer // true
```

### 原理

```js
function new_instance_of(leftVaule, rightVaule) { 
    let rightProto = rightVaule.prototype; // 取右表达式的 prototype 值
    leftVaule = leftVaule.__proto__; // 取左表达式的__proto__值
    while (true) {
    	if (leftVaule === null) {
            return false;	
        }
        if (leftVaule === rightProto) {
            return true;	
        } 
        leftVaule = leftVaule.__proto__ 
    }
}
```

`instanceof` 在查找的过程中会遍历左边变量的原型链，直到找到右边变量的 `prototype`，如果查找失败，则会返回 false，告诉我们左边变量并非是右边变量的实例。

> __proto__是什么？ JavaScript 对象的隐式原型属性

![原型关系图](https://user-gold-cdn.xitu.io/2018/5/28/163a55d5d35b866d?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

> 从这张经典的图中可以看出，除了 Function 外，构造函数显式的 prototype 和隐式的 __proto__ 指向并不一致，而最终，所有的 __proto__ 都指向了 Object.prototype （原型链），而 Object.prototype 的 __proto__ 指向 null

> 这里可以做一个思考，`Foo instanceof Foo`会返回什么呢？

如果回答 true 就错了，Foo 函数的 `prototype` 属性是 `Foo.prototype`，而 Foo 的 `__proto__` 属性是 `Function.prototype`，由图可知，Foo 的原型链上并没有 `Foo.prototype` ，因此 `Foo instanceof Foo` 也就返回 false 

而`Foo instanceof Function`和``Foo instanceof Object`均返回 true

# 3. 使用toString方法判断类型

还有一个不错的判断类型的方法，就是Object.prototype.toString，我们可以利用这个方法来对一个变量的类型来进行比较准确的判断

```js
Object.prototype.toString.call(1) // "[object Number]" 
Object.prototype.toString.call('hi') // "[object String]" Object.prototype.toString.call({a:'hi'}) // "[object Object]" Object.prototype.toString.call([1,'a']) // "[object Array]" 
Object.prototype.toString.call(true) // "[object Boolean]" 
Object.prototype.toString.call(() => {}) // "[object Function]" Object.prototype.toString.call(null) // "[object Null]" Object.prototype.toString.call(undefined) // "[object Undefined]" Object.prototype.toString.call(Symbol(1)) // "[object Symbol]"
```

# 4. 总结

我们使用 `typeof` 来判断基本数据类型是 ok 的，不过需要注意当用 `typeof` 来判断 `null` 类型时的问题，如果想要判断一个对象的具体类型可以考虑用 `instanceof`，但是 `instanceof` 也可能判断不准确，比如一个数组，他可以被 `instanceof` 判断为 Object。所以我们要想比较准确的判断对象实例的类型时，可以采取 `Object.prototype.toString.call` 方法。

参考：https://juejin.im/post/5b0b9b9051882515773ae714
