---
title: webpack4.0学习笔记
date: 2019-04-26T16:21:00
toc: false
tags: 
  - webpack
  - 工具
---

----

## webpack是什么

> 本质上，webpack 是一个现代 JavaScript 应用程序的静态模块打包器(module bundler)。当 webpack 处理应用程序时，它会递归地构建一个依赖关系图(dependency graph)，其中包含应用程序需要的每个模块，然后将所有这些模块打包成一个或多个 bundle。

## 为什么要用webpack


## 常见功能

### 加载css 

在js模块中import一个css文件

- style-loader
- css-loader

### 加载图片 加载字体

- file-loader

### 加载数据

- csv-loader
- xml-loader



## 常用的loader
loader是有执行顺序的，默认的执行顺序是从右至左，从下至上执行。
loader的类型：

1. 前置loader pre
2. 普通loader normal
3. 后置loader post
4. 内联loader

### css-loader



### style-loader

### less-loader

less less-loader

### sass-loader

node-sass sass-loader

### stylus-loader

stylus stylus-loader

### postcss-loader

postcss-loader autoprefixer

### babel

babel-loader @babel/core @babel/preset-env

### eslint

eslint-loader 

### expose-loader

代码中引入第三方模块，例如jquery，把jquery暴露给window

### file-loder 图片打包

file-loader默认会在内部生成一张图片到build目录下，并且把生成的图片名字返回回来

## 常用的plugin

### html-webpack-plugin

动态生成一个html，并且这个html会加载当前打包完成后的所有bundle.js。

### clean-webpack-plugin

清理dist目录，每次打包前都会把上一次打包的文件删除掉。

### mini-css-extract-plugin

### optimize-css-assets-webpack-plugin

压缩css 并且js还需要去压缩 使用uglifyjs-webpack-plugin

### webpack.ProvidePlugin 

在代码中引入第三方模块，例如jquery，可以在每个模块中引入jquery

[ProvidePlugin](https://webpack.docschina.org/plugins/provide-plugin/)

## 常用功能

- 错误代码定位

source map: 方便追踪错误代码或者警告在源代码中的原始位置。

- devServer

webpack-dev-server: 提供一个简单的web服务器，并且能够实时重新加载。

- 模块热替换

Hot Module Replacement: 热更新

- 压缩代码

设置mode为production

uglifyjs-webpack-plugin

- 打包后自动生成html

html-webpack-plugin 

html-webpack-template

- bundle分析

webpack-chart: webpack 数据交互饼图。

webpack-visualizer: 可视化并分析你的 bundle，检查哪些模块占用空间，哪些可能是重复使用的。

webpack-bundle-analyzer: 一款分析 bundle 内容的插件及 CLI 工具，以便捷的、交互式、可缩放的树状图形式展现给用户。


## loader和plugin的区别

loader

>webpack允许我们使用loader来处理文件，loader是一个导出为function的node模块。可以将匹配到的文件进行一次转换，同时loader可以链式传递。

plugin 

> webpack的plugin比loader强大，通过钩子可以涉及整个构建流程，可以做一些在构建范围内的事情。

## module和chunk的区别