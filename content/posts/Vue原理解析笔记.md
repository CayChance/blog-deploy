---
title: Vue原理解析笔记
date: 2019-03-27T19:09:06
toc: false
tags: 
  - vue
---
----

把数据渲染到页面发生了什么？

### new Vue()

###  init初始化 
**作用：** 合并配置、初始化生命周期、初始化事件中心、初始化渲染、data、 props、 methods、 computed 与 watcher 等

* 通过 Object.defineProperty 设置 setter 与 getter 函数，用来实现「响应式」以及「依赖收集」
* 如果有el属性，就调用vm.$mount方法挂载vm。(PS:vm就是Vue的实例)

### $mount挂载组件
**作用：** 挂载的目标就是把模板渲染成最终的 DOM

* 限制el，Vue不能挂载在body或者html这样的根节点上。
* 如果**没有render方法，会把el或template字符串转换成render方法**。(PS:把模板template/el编译生成render,详见compile步骤)
* $mount 方法实际上会去调用 mountComponent
* mountComponent 核心就是先实例化一个渲染Watcher
* mountComponent 的回调函数中会调用 updateComponent 方法
* updateComponent 方法中调用 vm._render 方法先生成虚拟 Node，最终调用 vm._update 更新 DOM。
* 核心方法 **vm._render**和**vm._update**
* Watcher 在这里起到两个作用，一个是初始化的时候会执行回调函数，另一个是当 vm 实例中的监测的数据发生变化的时候执行回调函数。

### compile编译
(PS:compile编译步骤不一定是必有的。如果没有使用template，统一都用的render，那么就会跳过此步骤。)
**作用：** 把模板template解析为AST，优化AST，把优化后的AST转换成可执行的代码。(PS:抽象语法树)

* parse
  **作用：** 把 template 模板字符串转换成 AST 树
* optimize
  **作用：** 优化语法树
  1.把一些 AST 节点优化成静态节点(1.标记静态节点;2.标记静态根)
  2.目的：如果是静态节点则它们生成 DOM 永远不需要改变，这对运行时对模板的更新起到极大的优化作用。
* generate
  **作用：** 把优化后的 AST 树转换成可执行的代码，即生成render function。
   
### render渲染函数
**作用：** 把实例渲染成一个虚拟DOM。(VNode)

* vm._render 最终是通过执行 createElement 方法并返回的是 vnode
* vm.$createElement 方法定义是在执行 initRender 方法的时候
* 除了 vm.$createElement 方法，还有一个 vm._c 方法
* vm._c 被模板编译成的 render 函数使用
* vm.$createElement 是用户手写 render 方法使用的
* 利用 createElement 方法创建 VNode
> createElement 创建 VNode 的过程，每个 VNode 有 children，children 每个元素也是一个 VNode，这样就形成了一个 VNode Tree，它很好的描述了我们的 DOM Tree。

``` js
export function initRender (vm: Component) {
  // ...
  // bind the createElement fn to this instance
  // so that we get proper render context inside it.
  // args order: tag, data, children, normalizationType, alwaysNormalize
  // internal version is used by render functions compiled from templates
  vm._c = (a, b, c, d) => createElement(vm, a, b, c, d, false)
  // normalization is always applied for the public version, used in
  // user-written render functions.
  vm.$createElement = (a, b, c, d) => createElement(vm, a, b, c, d, true)
}
```
    
### update方法
**作用：** 把VNode渲染成真实的DOM

* Virtual DOM映射到真实的dom，需要经历VNode的create,diff,patch等过程。
* 被调用的时机有两个。1首次渲染；2数据更新的时候
* _update 的核心就是调用 `vm.__patch__` 方法，这个方法实际上在不同的平台上的定义是不一样的。
* 在浏览器端渲染中，指向patch方法；在服务端渲染中，没有真实的浏览器DOM环境，所以不需要把VNode转成DOM，因此是一个空函数。

``` js
// install platform patch function
Vue.prototype.__patch__ = inBrowser ? patch : noop
```
``` js
Vue.prototype._update = function (vnode: VNode, hydrating?: boolean) {
  // ...
  // Vue.prototype.__patch__ is injected in entry points
  // based on the rendering backend used.
  if (!prevVnode) {
    // initial render
    vm.$el = vm.__patch__(vm.$el, vnode, hydrating, false /* removeOnly */)
  } else {
    // updates
    vm.$el = vm.__patch__(prevVnode, vnode)
  }
}
```

### patch方法
* patch
* patchVnode
* updateChildren
[参考diff算法](https://caychance.github.io/2019/04/03/diff%E7%AE%97%E6%B3%95/#patch%E6%96%B9%E6%B3%95)

### 小结
new Vue() => init => mount => compile => render => VNode => update => patch => dom

![](https://blog-pics.pek3b.qingstor.com/006tNc79ly1g2v2c39ruvj311e0rudpq.jpg)

<!-- ## 响应式原理
Vue.js 是采用 Object.defineProperty 的 getter 和 setter，并结合观察者模式来实现数据绑定的。当把一个普通 Javascript 对象传给 Vue 实例来作为它的 data 选项时，Vue 将遍历它的属性，用 Object.defineProperty 将它们转为 getter/setter。getter 做的事情是依赖收集，setter 做的事情是派发更新 -->


### 参考
[参考链接-Vue.js 技术揭秘](https://ustbhuangyi.github.io/vue-analysis/)

[参考链接-剖析 Vue.js 内部运行机制](https://juejin.im/book/5a36661851882538e2259c0f/section/5a37bbb35188257d167a4d64)