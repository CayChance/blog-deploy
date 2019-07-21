---
title: diff算法
date: 2019-04-02T19:08:09
toc: false
tags: 
  - vue
  - diff算法
---
---- 

### Virtual DOM 

因为如果在项目中大量的操作dom会很影响性能，所以Vue和React都尝试使用虚拟dom（virtual dom）。虚拟dom的本质其实就是用一个对象去描述整个dom结构，包括：当前dom的tag标签，data，children子节点，text文本，elm，key等等等，属性很多，就不一一列举了。

virtual dom真的比DOM操作效率高吗？不一定，那使用virtual dom的优势是什么呢？

使用虚拟dom的优势：

1. 不用手动频繁操作dom
2. 框架跨平台
3. 可以更好的实现SSR，同构渲染
4. 组件的高度抽象化

总之，**大大的提高了我们的工作效率。**

``` JavaScript
export default class VNode {
  tag: string | void;
  data: VNodeData | void;
  children: ?Array<VNode>;
  text: string | void;
  elm: Node | void;
  ns: string | void;
  context: Component | void; // rendered in this component's scope
  key: string | number | void;
  componentOptions: VNodeComponentOptions | void;
  componentInstance: Component | void; // component instance
  parent: VNode | void; // component placeholder node

  // strictly internal
  raw: boolean; // contains raw HTML? (server only)
  isStatic: boolean; // hoisted static node
  isRootInsert: boolean; // necessary for enter transition check
  isComment: boolean; // empty comment placeholder?
  isCloned: boolean; // is a cloned node?
  isOnce: boolean; // is a v-once node?
  asyncFactory: Function | void; // async component factory function
  asyncMeta: Object | void;
  isAsyncPlaceholder: boolean;
  ssrContext: Object | void;
  fnContext: Component | void; // real context vm for functional nodes
  fnOptions: ?ComponentOptions; // for SSR caching
  devtoolsMeta: ?Object; // used to store functional render context for devtools
  fnScopeId: ?string; // functional scope id support

  ...
}
```

### diff算法
* Vue的virtual dom的算法是基于Snabbdom库。
* VNode只会同层级比较，不会跨层级比较，因此复杂度为O(n)。
* 重点是`patch`方法。

### patch方法
注释：vnode：新的虚拟节点 oldVnode：旧的虚拟节点

1. vnode不存在但是oldVnode存在，需要销毁oldVnode
2. oldVnode不存在但是vnode存在，创建新节点
3. oldVnode和vnode都存在
3.1 oldVnode和vnode是同一个节点 执行`patchVnode`方法
3.2 vnode创建真实dom并替换oldVnode.elm

### patchVnode方法
1. oldVnode和vnode完全一致，则不需要做任何事情
2. vnode是文本节点或注释节点，但是vnode.text != oldVnode.text时，只需要更新vnode.elm的文本内容就可以
3. oldVnode和vnode都是静态节点，且具有相同的key，当vnode是克隆节点或是v-once指令控制的节点时，只需要把oldVnode.elm和oldVnode.child都复制到vnode上即可
4. vnode不是文本节点或注释节点
  <br>4.1 如果oldVnode和vnode都有子节点，且2方的子节点不完全一致，就执行`updateChildren`方法
  <br>4.2 只有oldVnode有子节点，那就把这些节点都删除
  <br>4.3 如果只有vnode有子节点，那就创建这些子节点
  <br>4.4 如果oldVnode和vnode都没有子节点，但是oldVnode是文本节点或注释节点，就把vnode.elm的文本设置为空字符串

### updateChildren方法
1. oldStartIdx>oldEndIdx 
  <br>oldCh已经遍历完了，但是newCh还没有，把newStartIdx-newEndIdx之间的vnode都是新增的，把这些vnode添加到oldCh末尾
2. newStartIdx>newEndIdx 
  <br>newCh已经遍历完了，但是oldCh还没有，把oldCh中oldStartIdx-oldEndIdx之间的vnode删除
3. oldStartIdx <= oldEndIdx && newStartIdx <= newEndIdx 
  <br>一直循环判断
  <br>3.1 oldVnode第一个child不存在，oldStart索引右移
  <br>3.2 oldVnode最后一个child不存在，oldEnd索引左移
  <br>3.3 oldStartVnode和newStartVnode是同一个节点，`patchVnode`两个节点，索引右移
  <br>3.4 oldEndVnode和newEndVnode是同一个节点，`patchVnode`两个节点，索引左移
  <br>3.5 oldStartVnode和newEndVnode是同一个节点，移动oldStartVnode到oldEndVnode节点后面
  <br>3.6 oldEndVnode和newStartVnode是同一个节点，移动oldEndVnode到oldStartVnode前面
  <br>3.7 oldChildren中寻找跟newStartVnode具有相同key的节点，如果找不到相同key的节点，说明newStartVnode是一个新节点，就创建一个，然后把newStartVnode设置为下一个节点
  <br>3.8 如果找到了相同key的节点，比较两个节点是否属于同一个节点，如果属于同一节点，就`patchVnode`，否则新创建节点

### 什么要设置key
如果不设置key的话，newCh和oldCh只会头尾两端比较，并且交叉比较。
如果设置key的话，除了上述的比较外，还会从用key生成的对象oldKeyToIdx中查找匹配的节点，所以为节点设置key可以更高效的利用dom。

### 疑问
virtual dom是vue 2.0以后增加的，那么2.0之前vue这一块是怎么处理的呢？

### 参考链接

[参考链接-Vue采用虚拟DOM的目的是什么?](https://www.zhihu.com/question/271485214)

[参考链接-Vue中virtual dom算法源码](https://github.com/vuejs/vue/blob/dev/src/core/vdom/patch.js)