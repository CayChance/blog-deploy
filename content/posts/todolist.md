---
title: "Todolist"
date: 2019-06-06T13:07:46+08:00
draft: false
toc: false
images:
tags: 
  - 杂货铺
---
----

刚接触新项目，有不少疑惑。在此列出来，后续做完项目或者做项目的过程当中，要把这些问题弄明白。

1. 之前都是用vue做的spa应用，这边使用vue做的是单/多页模板。这一块怎么配置的
2. 使用的是hash的路由配置规则
3. 脚手架是在vue cli3的基础上做的。需要深入学习一下vue cli3
4. 项目中的sass只需要 `npm install -D sass-loader node-sass` 即可用。不需要webpack手动配置？ 
5. 改进一下自己的编程风格。主要就是平时写代码的时候，加上详细的注释。

``` js
/**
 * get 提交
 * @param {String} url 请求的url
 * @param {any} params  请求的参数
 * @param {Obejct} config  请求配置
 * @returns Promise
 */
export function get(url, params = {}, config = {}) {
  let opts = {...config}
  opts.params = params
  return fetch.get(url, opts)
}
```

