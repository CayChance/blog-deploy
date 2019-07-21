---
title: label标签语义化
date: 2018-11-30T16:17:03
toc: false
tags: 
  - label
  - html
---
----

今天做一个问卷调研的需求的时候，如下图。用户点击A选项的文字的时候被选中，而不是只能点击A前面的圆圈的时候。

![](https://blog-pics.pek3b.qingstor.com/006tNbRwly1fxq5xd2ppyj30j20dyjtt.jpg)

html部分，需要使用input+label标签，其中label的for属性和input的id属性使用同一个变量。如下图：

``` html
<input type="radio" :id="subItem" class="checkbox" v-model="answers[index]">
<label :for="subItem">{{subItem}}</label>
```

刚开始用的p标签，一直不好使。后来解决了以后，去搜了一下label标签的语义化

>
<label> 标签为 input 元素定义标注（标记）。
label 元素不会向用户呈现任何特殊效果。不过，它为鼠标用户改进了可用性。
如果您在 label 元素内点击文本，就会触发此控件。
就是说，当用户选择该标签时，浏览器就会自动将焦点转到和标签相关的表单控件上。
<label> 标签的 for 属性应当与相关元素的 id 属性相同。