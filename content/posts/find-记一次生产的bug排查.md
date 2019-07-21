---
title: find-记一次生产的bug排查
date: 2019-01-18T21:14:12
toc: false
tags: 
  - bug
---
----

### 前提概要
上线一周，最近的用户量增加了一些，今天下午的时候，群里一直被@，有一部分用户打开产品流程首页的时候白屏。

### bug复现
借了用户的账号密码登录以后发现，我手机上是可以打开的。然后看了用户的手机系统是安卓5.0，不是很高的系统。然后找了一个测试的5.0的手机试了一下，还是可以打开的。反映出问题的用户中，vivo的机型比较多。然鹅，我们这边vivo的测试机没有5.0以下版本的。后来接到了一个魅族和小米的手机，分别是5.0和4.4的系统。登录账号，发现bug复现了。

### 问题定位
1.装了生产的包，打开chrome的调试功能(chrome://inspect/#devices);发现webpack打包以后的main文件里面的1行3000多列出现的报错。去代码中定位了一下，发现了这段代码。
``` js
let stepObj = allStep.find((item) => {
  return item.routeName === currentName;
});
```

2.chrome模拟打开页面的时候，在控制台输入`navigator.userAgent`，发现这个浏览器的是chrome 43。chrome43支持find语法吗？

3.去[caniuse](https://caniuse.com/#search=find)中搜索find的兼容性，发现chrome44以后的版本才支持find语法。

至此，问题已经定位到了。我们使用了find语法，但是用户的浏览器版本太低不支持。但是我们项目中使用了babel了呀?

### 问题解决
但是我们项目中使用了babel了呀?

虽然使用了babel，但是没有配置转换find语法。

google搜索babel find然后找到一条Stack Overflow的[帖子](https://stackoverflow.com/questions/32401513/array-find-doesnt-work-with-babel)。解决方法也列出来了。

至此，结束。

### 后记
当然了，还没有结束呢。

页面可以打开了，但是又出现另一个问题了。等着下周客户端一起检查一下。

### 心得

1. 遇到bug心态一定要保持好，用我同事的话，你不觉得这样子一步步的去定位bug，跟侦探破案一样刺激吗？（PS：感谢他，以上有很多都是他发现的，受益匪浅）。

2. 用好google搜索，Stack Overflow,github等这些国外技术社区，真的会收益良多。（PS：百度是真的垃圾，此处略去一万字）

3. 英语好真的很重要啊。那天看了一篇文章，英语对一个程序员来说重要吗？回答是，如果英语不好不影响你做一个程序员；但是你想学好一门编程语言，那英语实在是太重要了。