---
title: JSX in Vue
date: 2019-03-08T23:53:26
toc: false
tags: 
  - jsx
---
----

### 使用Vue的template遇到的问题

前几天接了一个需求-APP首页配置化。大体思路就是，后台做一个页面，这个页面上提供了N个模板，然后运营人员可以动态的添加模板，并且给每个模板进行不同的配置。比如，轮播图模板，轮播图模板可以配置一个标题，以及描述，模板可以配置多张图片，图片可以调整顺序，点击图片跳转的链接等。

我们第一期提供了10个模板。

``` html
<template>
  <template v-if="templateCode==='SCOELL_IMG_01'">
    <c-scroll-img></c-scroll-img>
  </template>

  <template v-if="templateCode==='TWO_IMG_01'">
    <c-two-img></c-two-img>
  </template>

  <template v-if="templateCode==='TWO_IMG_02'">
    <c-two-img></c-two-img>
  </template>

  <template v-if="templateCode==='THREE_IMG_01'">
    <c-three-img></c-three-img>
  </template>

  <template v-if="templateCode==='THREE_IMG_02'">
    <c-three-img></c-three-img>
  </template>
</template>
```

我举例说明，先写5个说明一下问题。有一个比较严重的问题，就是`v-if`用的太多了。这里面不能嵌入js，如果以后提供20个模板甚至更多呢？template这种方式肯定会写的很痛苦！

### 可以尝试使用JSX解决

所以，我们可以试试用另一种方式去写组件-JSX，在Vue中使用JSX。JSX是React的核心组成部分。Vue2.0以后才开始支持JSX的。

### JSX介绍

JSX其实就是使用JavaScript去表示dom元素结构。

**所谓的 JSX 其实就是 JavaScript 对象。**

1. JSX 是 JavaScript 语言的一种语法扩展，长得像 HTML，但并不是 HTML。
2. React.js 可以用 JSX 来描述你的组件长什么样的。
3. JSX 在编译的时候会变成相应的 JavaScript 对象描述。
4. react-dom 负责把这个用来描述 UI 信息的 JavaScript 对象变成 DOM 元素，并且渲染到页面上。

从 JSX 到页面到底经过了什么样的过程

![](https://blog-pics.pek3b.qingstor.com/006tKfTcly1g11dfngojkj30hm075aaq.jpg)


#### JSX基本语法

在Vue中使用JSX首先需要保证Vue的版本大于2.0，然后官方的[jsx](https://github.com/vuejs/jsx)包,这个包支持的是Babel 7+；
如果是Babel 6，则需要安装[babel-plugin-transform-vue-jsx](https://github.com/vuejs/babel-plugin-transform-vue-jsx)包。

然后根据文档安装完以后。

### JSX VS Template

1. JSX其实就是JavaScript对象，Template是Vue组件中render方法的输入。
2. JSX特别利于理解，相比较Template而言。（虽然写惯了Template，JSX的语法还需要慢慢适应。）
3. Tempplate对初学者比较友好。