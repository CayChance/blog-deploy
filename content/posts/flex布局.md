---
title: flex布局
date: 2018-11-28T17:06:01
toc: false
tags: 
  - css
  - flex
  - bug
---
----

### flex的一个兼容问题

flex布局中的justify-content: space-evenly;在部分安卓手机上是不支持这个属性的。

``` css
justify-content: space-evenly
```

### 解决方案

可以使用space-around或者space-between配合margin一起使用达到space-evenly的效果

``` css
justify-content: space-around
```

``` css
justify-content: space-between
```