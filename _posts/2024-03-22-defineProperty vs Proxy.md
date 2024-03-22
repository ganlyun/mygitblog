# defineProperty vs Proxy

vue2到vue3的一大变化就是响应式的实现从defineProperty改为了Proxy，这样做的好处到底在哪里呢？

## 代理的粒度

defineProperty只能代理属性，Proxy代理整体对象

> 对于嵌套的对象，两者都需要递归代理

## 性能

因为defineProperty 需要遍历进行劫持，所以有性能损耗

## 对象

defineProperty 无法实现对新添加的属性进行劫持，删除属性的方法也劫持不到，这就是为什么vue2中提供了Vue.set和Vue.delete方法，就是为了在添加新属性和删除属性的时候保持响应性

## 数组

defineProperty 无法代理可以修改数组的多种API（push、sort等），需要额外的代码进行处理（vue2重写了这些API）

defineProperty 无法劫持length这种设置configurable=false的属性，这就是为什么[vue2关于响应式的文档](https://v2.cn.vuejs.org/v2/guide/reactivity.html)中说当你修改length时Vue无法检测到变动

> 有很多文章说，defineProperty不支持对按数组下标赋值的变动劫持，但是通过实际的代码测试，完全是可以的，与对象属性效果一致。
>
> 所以，defineProperty 代理的数组，同样预备响应性，但是vue2没有这么做，这里是尤大的回答[为什么vue没有提供对数组属性的监听 · Issue #8562 · vuejs/vue (github.com)](https://github.com/vuejs/vue/issues/8562)——因为性能问题，性能代价和获得的用户体验收益不成正比。



## 总结

使用defineProperty 实现响应式本质是一种hack行为，因为设置了getter/setter其实就已经是语言层面的修改

ECMAScript为了应对这种需求，在ES6标准中提供了Proxy对象，相当于暴露出一个更标准更完备的接口供开发者使用，而不是通过defineProperty “曲线救国”



## 附代码

```js
// defineProperty 
function defineReactive(obj, key, val) {
  Object.defineProperty(obj, key, {
    get() {
      console.log(`获取 ${key}:${val}`);
      return val;
    },
    set(newVal) {
      if (newVal !== val) {
        console.log(`设置 ${key}:${newVal}`);
        val = newVal;
      }
    },
  });
}

// Proxy
export function reactive(obj) {
  if (typeof obj !== 'object' && obj != null) {
    return obj;
  }
  // Proxy相当于在对象外层加拦截
  const observed = new Proxy(obj, {
    get(target, key, receiver) {
      const res = Reflect.get(target, key, receiver);
      console.log(`获取${key}:${res}`);
      return res;
    },
    set(target, key, value, receiver) {
      const res = Reflect.set(target, key, value, receiver);
      console.log(`设置${key}:${value}`);
      return res;
    },
    deleteProperty(target, key) {
      const res = Reflect.deleteProperty(target, key);
      console.log(`删除${key}:${res}`);
      return res;
    },
    has(target, key) { 
      const res = Reflect.has(target, key);
      console.log(`存在${key}:${res}`);
      return res;
    }
  });
  return observed;
}

```

在Proxy响应式示例中，我们看到的每个方法都调用了Reflect的同名方法——这个方法代表着语言的默认行为

Proxy提供的13个handler方法均有与之对应的Reflect方法

Reflect的所有属性和方法都是静态的，它不是一个函数对象，不能通过new进行构造

Reflect产生的目的是将Object上一些明显属于语言内部的方法放到自己身上，比如Object.defineProperty，

也就是说，从`Reflect`对象上可以拿到语言内部的方法。
