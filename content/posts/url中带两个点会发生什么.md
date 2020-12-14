---
title: "url中带两个点会发生什么"
date: 2020-12-14T22:21:50+08:00
draft: false
toc: false
images:
tags: 
  - 总结
---
----

很久没有更新了~

#### 背景：
最近在做一个B端的内部应用系统，查找通讯录。遇到一个问题，觉得很有意思，在此记录一下。后端提供了几个接口来查询通讯录。接口地址大概是这样子的：
``` js
url = `http://aa.bbb.com/user/search/${page}/${size}/${val}`
```

#### 解释：
get请求，要查询的数据就是val。前几天前端做了限制，不让用户输入一些特殊字符，今天产品说这块不做限制了。因此遇到了一个问题。

#### 现象：
测试过程中当输入两个点的时候，接口请求404。

#### 分析：
正常请求地址：http://aa.bbb.com/user/search/1/6/..

浏览器中的实际访问地址：http://aa.bbb.com/user/search/1  因此导致接口返回404

#### 可亲自尝试一下：
在浏览器中访问： https://www.zhihu.com/question/422032638/answer/1522362574

可以访问，虽然提示内容已删除。

在浏览器中访问： https://www.zhihu.com/question/422032638/answer/../1522362574

但是实际的访问地址变成：  https://www.zhihu.com/question/422032638/1522362574

#### 解释：

[url中的点.有什么含义](https://stackoverflow.com/questions/6008829/what-does-a-dot-mean-in-a-url-path?lq=1)

[规范](https://tools.ietf.org/html/rfc3986#section-5.2.4)


