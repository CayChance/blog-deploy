---
title: Vuex存储的时候遇到的问题及解决方案
date: 2019-01-22T19:40:06
toc: false
tags: 
  - vuex
---
----

### 引子
使用Vuex遇到过两个问题。

1. 页面刷新或者跳转到第三方再回来，数据是会丢掉的。即Vuex持久化存储问题。
2. 在Vuex中存储一个Falsey值（PS：false,'',null,undefined,NaN）的时候，Vuex中存储不了。

### 解决
第一个问题，网上有很多解决方案。使用[vuex-persistedstate](https://github.com/robinvdvleuten/vuex-persistedstate)。具体看文档，我就不细说了，可以使用localStorage,也可以使用cookie。

第二个问题，其实并不是Vuex本身的问题，而是因为我们项目中统一为每一个state字段生成了mutations方法。

generate方法中的语句`state[name] = value || state[name];`判断，value为false的时候，确实存不进来。

然后自己重新写了一个
``` js
updateToken(state,newToken){
  state.token = newToken;
}
```


``` js
...

const options = {

  // 多页面共享数据
  state: {
    aaa: '',
    bbb:'',
    ccc: '',
  },

  // 数据变更
  mutations: {
    updateToken(state,newToken){
      state.token = newToken;
    }
  },
}

// 生成 mutations 方法
function generate(name) {
  return function (state, value) {
    state[name] = value || state[name];
  }
}

// 为每个 state 字段生成对应的 mutations 方法
Object.keys(options.state).forEach((key) => {
  options.mutations[key] = generate(key);
});
...
export default store;
```
