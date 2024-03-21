# ref vs reactive



## 简单认识

首先我们知道，ref一般用于原始类型，reactive用于引用类型

ref也可以用于引用类型，如果使用引用类型，ref会通过reactive将对象转换成响应式代理对象

而reactive如果用于原始类型，控制台会给出警告——”value can not be reactive"，如果配置了ts也会提示reactive只接受对象类型的参数

## 实现原理

### ref

> 参考 [core/packages/reactivity/src/ref.ts at main · vuejs/core (github.com)](https://github.com/vuejs/core/blob/main/packages/reactivity/src/ref.ts)

ref是通过类的存取函数的修改实现的

> 对象中也有访问器属性，参考[JavaScript 数据类型和数据结构 - JavaScript | MDN (mozilla.org)](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Data_structures#object)

> 官方文档
>
> 在标准的 JavaScript 中，检测普通变量的访问或修改是行不通的。然而，我们可以通过 getter 和 setter 方法来拦截对象属性的 get 和 set 操作。



### reactive

> 参考 [core/packages/reactivity/src/reactive.ts at main · vuejs/core (github.com)](https://github.com/vuejs/core/blob/main/packages/reactivity/src/reactive.ts)

通过Proxy实现

> 官方文档
>
> reactive() 将深层地转换对象：当访问嵌套对象时，它们也会被 reactive() 包装。当 ref 的值是一个对象时，ref() 也会在内部调用它。
>
> 为保证访问代理的一致性，对同一个原始对象调用 `reactive()` 会总是返回同样的代理对象，而对一个已存在的代理对象调用 `reactive()` 会返回其本身
>
> 对响应式对象进行解构操作会丢失响应性

## 深度响应性

深度响应性可以通过通过使用 [`shallowRef()`](https://cn.vuejs.org/api/reactivity-advanced.html#shallowref) 和 [`shallowReactive()`](https://cn.vuejs.org/api/reactivity-advanced.html#shallowreactive) 来绕开。

浅层式 API 创建的状态只在其顶层是响应式的，对所有深层的对象不会做任何处理。

## 关于自动解包

> 与 reactive 对象不同的是，当 ref 作为响应式数组或原生集合类型 (如 `Map`) 中的元素被访问时，它**不会**被解包：
>
> ```js
> const books = reactive([ref('Vue 3 Guide')])
> // 这里需要 .value
> console.log(books[0].value)
> 
> const map = reactive(new Map([['count', ref(0)]]))
> // 这里需要 .value
> console.log(map.get('count').value)
> ```



在模板渲染上下文中，只有顶级的 ref 属性才会被解包。
