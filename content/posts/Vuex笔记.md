---
title: Vuex笔记
date: 2019-03-28T16:21:30
toc: false
tags: 
  - vuex
---
----

### Vuex概述

Vuex 是一个专为 Vue.js 应用程序开发的**状态管理**模式。

简单来说，就是存储页面中的各种数据。

### 为什么要用Vuex

Vuex的使用并不是必要的。

Vue.js中，数据之间都是在组件之间进行传递的，但是当你的应用变的很大或者很复杂的时候，会有两个问题。

1、当组件嵌套很深的时候，数据的传递，修改，其实都不太方便，很容易导致出错。

2、这些状态可能会在任何组件中被修改，不方便统一管理。尤其是多人合作开发的时候。

Vuex也是为了解决这两个问题而生的。

当然，有人可能会想到使用一个全局对象，再去上层封装了一些数据存取的接口来解决。Vuex和单纯的全局对象有以下2点不同：

* Vuex 的状态存储是响应式的。当 Vue 组件从 store 中读取状态的时候，若store中的状态发生变化，那么相应的组件也会相应地得到高效更新。

* 不能直接改变store中的状态。改变store中的状态的唯一途径就是显式地提交(commit)mutation。这样使得我们可以方便地跟踪每一个状态的变化。

### Vuex详细介绍

Vuex中有五个核心的概念：

* State
    
    > 用于存储数据

* Getter
    
    > 可理解为store的（computed）计算属性

    > Getter可接受state和getter两个参数。
    
* Mutation
    
    > 更改 Vuex 的 store 中的状态的唯一方法是提交 mutation。

    > mutation 都是同步事务

* Action
    
    > Action类似mutation。
    
    >区别有两点：1.Action可以包含异步操作；2.Action 提交的是 mutation，而不是直接变更状态。
* Module
    
    > 由于使用单一状态树，应用的所有状态会集中到一个比较大的对象。当应用变得非常复杂时，store 对象就有可能变得相当臃肿。Vuex 允许我们将 store 分割成模块（module,每个模块拥有自己的state、mutation、action、getter以及嵌套子模块。

### 总结
* 每一个 Vuex 应用的核心就是store（仓库）。“store”基本上就是一个容器，它包含着你的应用中大部分的状态(state)。
* 想修改state只能提交（commit）mutation。
* mutation中只能进行同步操作。
* 异步操作可以分发（dispatch）action，当然，action的实质还是提交（commit）mutation。

![](https://blog-pics.pek3b.qingstor.com/006tKfTcly1g1io1c6idkj30jh0fbwef.jpg)

### 一句话总结
Vuex的数据流向：
State 渲染到Vue Components,分发(Dispatch) Actions,提交(Commit) Mutations，修改(Mutate) State。