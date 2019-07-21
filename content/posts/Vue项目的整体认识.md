---
title: Vue项目的整体认识
date: 2019-01-25T21:08:20
toc: false
tags: 
  - vue
---

-----

### 引子
其实从16年11月28日进公司实习至今，已经两年多了。刚去公司的时候，我们当时前端就3个人，那会也是第一次接触Vue的。刚接触的感觉，就是，这玩意，这么神奇。然后平时做需求也是，能把需求做了就行。至今还记得第一次前后端联调，第一次项目上线。

虽然很早就接触了，但是我们项目的很多东西，我都没有认真的静下来去看。导致很多东西都是一知半解的。直到自己有一天幡然醒悟。

我们的项目是我们前端小组长搭建的一个脚手架,包括vue cli+webpack配置+node(express)。使用 node 代替 NGINX 提供请求代理、WebViewJavaScriptbridge 与客户端交互、Vue 全家桶作为开发框架，并且用 express 搭建了 mock 框架。

### 正题

首先，Vue没有什么神奇的，最后上线的东西就是一个dist文件，里面一个入口文件index.html+一个static文件，static文件里面是打包后的图片，css和js。

![](https://blog-pics.pek3b.qingstor.com/006tNc79ly1fzj6d8fhqvj30hk0k40ue.jpg)

以本地开发为例，`npm run dev` 以后，正常情况下会启动一个本地服务，然后当用户访问`localhost:8080/login`，本地服务器会返回打包后的`index.html`文件，index文件里面引入了main.js即js代码的主入口文件。

这部分是Vue项目中的node服务器处理的，这也是node服务器的第一个作用，方便本地调试。服务器会把收到的请求转到index.html。具体可参考作用1。


main.js文件里面其实会实例化一个Vue对象，并挂载在某个dom元素下。实例化的Vue对象里面有Vue Router和Vuex。Vuex按需要来决定要不要引用。Vue Router会在你访问某个路径的时候，比对当前的location.pathname和Vue Router中的某一个path是否可以匹配上，能够匹配上的话，会渲染对应的组件页面。

以上就是对Vue整个项目的一个认识。Vue项目中用到了node，说一下node的作用吧。

#### 作用1 

服务器会把收到的请求转到index.html。（PS：test环境和production环境是这样处理的。）

``` js
app.use(function (req, res) {
  res.sendFile(webpackConfig.output.path + '/index.html', {
    headers: {
      'Content-Type': 'text/html; charset=UTF-8'
    }
  });
});
```

其中，dev环境下使用webpack-dev-middleware中间件来做。这么做的其中一个好处是，不在磁盘中存储文件，而是直接生成在内存中。
``` js
var devMiddleware = require('webpack-dev-middleware')(compiler, {
  publicPath: webpackConfig.output.publicPath,
  stats: {
    colors: true,
    chunks: false
  }
})

// serve webpack bundle output
app.use(devMiddleware)
```

#### 作用2 

使用中间件代理解决跨域问题，我们用了一个proxy-middleware的中间件。

``` js
// proxy api requests
Object.keys(apiMap).forEach(key => {
  const api = apiMap[key];
  app.use(api.path, proxy(api.proxy));
});
```

#### 作用3 

请求一些静态资源，或者本地不经过webpack编译的图片。

``` js
// serve pure static assets
var staticPath = path.posix.join(config.dev.assetsPublicPath, config.dev.assetsSubDirectory);
app.use(staticPath, express.static(path.resolve(__dirname, '../static')))
```

#### 作用4 

本地调试的时候mock数据 else部分

``` js
if (argv.proxy) {
  Object.keys(apiMap).forEach(key => {
    const api = apiMap[key];
    app.use(api.path, proxy(api.proxy));
  });
}
// mock api requests
else {
  let mockDir = path.resolve(__dirname, '../mock');
  (function setMock(mockDir) {
    fs.readdirSync(mockDir).forEach(function (file) {
      var filePath = path.resolve(mockDir, file);
      var mock;
      if (fs.statSync(filePath).isDirectory()) {
        setMock(filePath);
      }
      else {
        if (/\.js$/.test(file)) {
          mock = require(filePath);
          app.use(mock.api, mock.response);
        }
      }
    });
  })(mockDir);
}
```

### 后记

这些话是说给自己的。

首先鼓励的话，从最开始看到项目那一堆代码时的一脸懵逼，到现在基本都明白了。（不说全部都明白了，但是就算有不知道的，也知道去搜索然后了解对应的部分是做什么的）。当然，这个经历的时间有些长，包括自己学习node，koa+express。这个过程也是很美好的，收获也不少。

然后警钟的话，

**爱一行，干一行；干一行，爱一行。**最开始虽然说是为了互联网行业的高薪来的，但是起码当时选的是你感兴趣的前端，（PS：这话说得可能就不对，因为真正的程序员眼里是不分前后端的），所以，爱一行，干一行。

**时刻保持着对知识的好奇以及极强的求知欲。** 这句话，自己好好反思。

最后，没有最后。加油！！！