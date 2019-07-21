---
title: "css3的动画"
date: 2019-06-13T19:54:06+08:00
draft: false
toc: false
images:
tags: 
  - css
---
----

最近在做一个中台门户网站，其中首页有不少动画效果。列举几个：

1. 鼠标悬浮div整体放大
2. 鼠标悬浮整个div上移
3. 鼠标悬浮第二个div向上运动覆盖第一个div

前公司有同事分享过css3中，2D转换，3D转换，过渡，动画之间的区别。

### 2D转换
2D转换主要用到`transform`属性，常用的值包括*移动*`translate(x,y)`,*旋转*`rotate()`,*缩放*`scale(x,y)`,*倾斜*`skew(x,y)`,前四者合集`matrix`。

### 3D转换
3D转换用的也是`transform`属性。
主要是移动，旋转，缩放三者从X，Y，Z轴进行变换，或者X，Y，Z的合集。

### 过渡 transition
用途：从一种样式转变到另一种样式。

使用注意的点：

1. 常用于添加hover触发动画，哪个元素是动画的触发点，hover就加给该元素。
2. 运动的元素需要添加`transition:width|all 3s;` 。
3. 两元素之间的关系可以是同级兄弟元素，也可以是父子元素。

### 动画
用途：创建动画。

使用：
``` css
div
{
	width:100px;
	height:100px;
	background:red;
	animation:myfirst 5s;
}

@keyframes myfirst
{
	from {background:red;}
	to {background:yellow;}
}
``` 

### 区别
列一下三者的区别，其实一看他们的英文意思，区别也就显而易见了。

- transform并不算真正意义上的动画，只是单纯做一下转变，你甚至都看不到这个转变的过程。
- transition常用于hover的简单动画，一般就hover前后的两个状态。
- animation属于真正意义上的动画，一般可以做复杂的动画。

| 属性 | 词义 | 异同 | 关注点 | 强调 | 帧动画 |
| :-: | :-: | :-: | :-: | :-: | :-: |
| transform | 转变 | 非动画 | 忽略 | 忽略 | 忽略 |
| transition | 过渡 | 简单动画 | 更关注css property的变化 | 强调过渡 | 只有两个关键帧 |
| animation | 动画 | 复杂动画 | 作用于元素本身而非样式属性 | 强调流程与控制 | 很多个关键帧 |
