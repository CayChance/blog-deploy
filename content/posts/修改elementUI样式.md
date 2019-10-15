---
title: "修改elementUI样式"
date: 2019-10-12T19:06:29+08:00
draft: false
toc: false
images:
tags: 
  - css
---
----

最近做一个需求，需要把之前写的系统样式全部修改成统一的设计样式。

由于之前的系统是使用element-ui做的，本身用的element-ui的组件不多，基本都是table,select,input等几个标签。

有两种思路：

1. 不考虑scoped的话，在全局写一个element-ui的样式，xxx.css或者xxx.scss，在全局引入该文件。

2. 考虑scoped的话，可以使用 /deep/ 或者 >>> 操作符。

因为开发的系统基本全部使用的element-ui的样式，基本没有自己写css，所以可以使用方法1。


使用 >>> 或者 /deep/ 示例

``` scss
<style lang="scss" scoped>
.home {
  // ***
}
.home /deep/ .skeleton{
  background: red;
  /deep/ .header {
    background: pink;
  }
  /deep/ .like-wrapper {
    background: #fff;
    /deep/ .title{
      /deep/ .img{
        border: 1px solid red;
      }
      /deep/ .text{
        width: 300px;
        border-top: 1px solid green;
      }
    }
  }
}
</style>
```
