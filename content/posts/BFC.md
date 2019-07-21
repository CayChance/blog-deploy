---
title: BFC
date: 2019-01-11T20:31:59
draft: false
toc: false
tags: 
  - css
  - bfc
---
----

### BFC是什么

前几天被人问到了BFC，一脸懵逼。赶紧看看，简单总结一下。

#### 概念：
> BFC 即 Block Formatting Contexts (块级格式化上下文)，它属于普通流。

PS:区别于普通流，常见的还有浮动（float），绝对定位（absolute）

**具有 BFC 特性的元素可以看作是隔离了的独立容器，容器里面的元素不会在布局上影响到外面的元素，并且 BFC 具有普通容器所没有的一些特性。**

#### BFC的特性：
1. 同一个BFC中的margin会重叠
2. 浮动的元素会脱离普通文档流，但是BFC却可以包含浮动的元素，即消除浮动。
3. BFC可以阻止元素被浮动元素覆盖。

#### 如何触发BFC：(PS：满足一下任一条件即可触发)
1. body根元素
2. 浮动元素：float除none以外的值
3. 绝对定位元素：position 等于absolute或者fixed
4. display等于 inline-block、table-cells、flex
5. overflow等于除了 visible 以外的值 (hidden、auto、scroll)
类似函数作用域。

[参考链接-10 分钟理解 BFC 原理](https://zhuanlan.zhihu.com/p/25321647)