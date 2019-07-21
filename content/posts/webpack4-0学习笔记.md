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

## 为什么要用webpack

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

### mini-css-extract-plugin

### optimize-css-assets-webpack-plugin

压缩css 并且js还需要去压缩 使用uglifyjs-webpack-plugin

### webpack.ProvidePlugin 

在代码中引入第三方模块，例如jquery，可以在每个模块中引入jquery

[ProvidePlugin](https://webpack.docschina.org/plugins/provide-plugin/)

## 在代码中引入第三方模块

1. expose-loader
2. webpack.ProvidePlugin
3. 引入不打包 

## 图片如何打包

1. 在js中引入
2. 在css中
3. 在html中

## loader和plugin的区别

## module和chunk的区别