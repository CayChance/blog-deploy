---
title: "Vue中相同页面跳转的问题及解决方案"
date: 2019-06-19T17:31:40+08:00
draft: false
toc: false
images:
tags: 
  - vue
---
----
### 问题

也是最近做需求遇到的一个问题。首页的一个总台系统。如图页面顶部的导航条，其中*解决方案*这一项下面的四项一期都暂时不做，等下一个排期再做。

![](https://blog-pics.pek3b.qingstor.com/f88c6218-9fc0-444b-af32-f9ed10347de5.png)

为了考虑用户体验，这四个点进去，都会进入同一个页面——敬请期待页面。

当然了，Vue中为了提高性能，相同的组件会被复用，也就是说，组件的生命周期钩子不会再次被调用。详细可参考-[响应路由参数的变化](https://router.vuejs.org/zh/guide/essentials/dynamic-matching.html#%E5%93%8D%E5%BA%94%E8%B7%AF%E7%94%B1%E5%8F%82%E6%95%B0%E7%9A%84%E5%8F%98%E5%8C%96)

>
提醒一下，当使用路由参数时，例如从 `/user/foo` 导航到 `/user/bar`，原来的组件实例会被复用。因为两个路由都渲染同个组件，比起销毁再创建，复用则显得更加高效。不过，这也意味着组件的生命周期钩子不会再被调用。

### 解决方案

当然，文档中也给出了对应的解决方案。

#### 方案1：

**必须要设置**该页面对应的router为动态路由。 `path: '/user/:id'`，否则也不会生效。

``` js
const User = {
  template: '...',
  watch: {
    '$route' (to, from) {
      // 对路由变化作出响应...
    }
  }
}
```

#### 方案2：

不知道是不是我用法不对：这个导航守卫只会触发一次。

``` js
const User = {
  template: '...',
  beforeRouteUpdate (to, from, next) {
    // react to route changes...
    // don't forget to call next()
  }
}
```

#### 方案3：

这个自己想了一个方法。

方法的弊端就是，如果多出页面涉及到该问题，需要依次去添加。

``` js
navigateTo (path) {
  if (!path) return;
  if (path === "/stay-tuned") {
    this.$router.push({ path });
    location.reload();
  } else {
    this.$router.push({ path });
  }
}
```