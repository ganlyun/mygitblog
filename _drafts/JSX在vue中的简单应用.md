JSX在vue中的简单应用
===

### 问题&处理

在antue的项目中实现anchor组件的过程中，遇到了一个问题，根据affix属性决定是否将Anchor组件包裹在Affix组件中，这个如果使用template着实让人无从下手，看了一下antd实现，使用JSX根本没有这个问题，这一点上，JSX确实要比固定的template模板灵活的多

翻了一下vue的文档，同样提供了对JSX的支持，于是果断抛弃template，尝试使用JSX去渲染DOM

antd中是这样的：

```javascript
  render() {
    const {
      prefixCls,
      className = '',
      style,
      offsetTop,
      affix,
      showInkInFixed,
      children,
    } = this.props;
    const { activeLink } = this.state;

    const inkClass = classNames(`${prefixCls}-ink-ball`, {
      visible: activeLink,
    });

    const wrapperClass = classNames(className, `${prefixCls}-wrapper`);

    const anchorClass = classNames(prefixCls, {
      'fixed': !affix && !showInkInFixed,
    });

    const anchorContent = (
      <div className={wrapperClass} style={style}>
        <div className={anchorClass}>
          <div className={`${prefixCls}-ink`} >
            <span className={inkClass} ref="ink" />
          </div>
          {children}
        </div>
      </div>
    );

    return !affix ? anchorContent : (
      <Affix offsetTop={offsetTop}>
        {anchorContent}
      </Affix>
    );
  }
```

最终我的代码中是这样使用的：

```JavaScript
  render (h) {
    const { prefixCls, baseCls, ballCls, offsetTop, affix } = this
    const anchorContent = (
      <div class={`${prefixCls}-wrapper`} style={{ maxHeight: '100vh' }}>
        <div class={baseCls}>
          <div class={`${prefixCls}-ink`}>
            <span class={ballCls} style={{ top: '10.5px' }} ref="ink"></span>
          </div>
          { this.$slots.default }
        </div>
      </div>
    )
    return !affix ? anchorContent : (
      <Affix offsetTop={offsetTop}>
        {anchorContent}
      </Affix>
    )
  }
```
首先，完全省略了<template/>，使用render方法，h是必传参数（babel-plugin-transform-vue-jsx@3.4.0后可以自动注入），具体可见官方文档说明，基本写法与react无异，就是在class和style上取消了v-bind:前缀，它们的属性值使用的{}包裹，像ref特性的用法依然保持原样

* 详细用法参见 [babel-plugin-transform-vue-jsx的GitHub主页](https://github.com/vuejs/babel-plugin-transform-vue-jsx)

这里面最需要注意的就是插槽的用法，直接写<slot/>是没有用的，必须使用{this.$slots.default}才可以

用是可以用了，但是问题来了：what is JSX?

### What is JSX?


```javascript
const element = <h1>Hello, world!</h1>;
```
直观一点上面就是JSX

* [印记中文react文档中的JSX介绍](https://react.docschina.org/docs/introducing-jsx.html)

#### 总结一下：
1. JSX是一种 JavaScript 的语法扩展
2. JSX中可以使用 JavaScript 的表达式
3. JSX本身也是 JavaScript 的表达式，在编译之后会被转化为普通的 JavaScript 对象，因此可以作为参数或者返回值
4. 你可以使用引号来定义以字符串为值的属性，也可以使用大括号来定义以 JavaScript 表达式为值的属性（即就是第二点中的“使用”了）
5. Babel 转译器会把 JSX 转换成一个名为 `React.createElement()` 的方法调用，所以，从本质上讲，JSX 只是为 `React.createElement(component, props, ...children)` 函数提供的语法糖（这一点在vue里面也是一样，下面的引用来自vue文档）

> `createElement` 到底会返回什么呢？其实不是一个_实际的_ DOM 元素。它更准确的名字可能是 `createNodeDescription`，因为它所包含的信息会告诉 Vue 页面上需要渲染什么样的节点，及其子节点。我们把这样的节点描述为“虚拟节点 (Virtual Node)”，也常简写它为“VNode”。“虚拟 DOM”是我们对由 Vue 组件树建立起来的整个 VNode 树的称呼。

到这里，基本就理解JSX了，更进一步的，就是如何实现一个JSX了，先挖个坑吧

