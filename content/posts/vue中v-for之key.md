---
title: "Vue中v-for之key"
date: 2019-10-23T20:02:37+08:00
draft: false
toc: false
images:
tags: 
  - vue
---
----

最近的在帮别人维护代码的过程中遇到一个bug。使用v-for循环的时候，key设置的是index。

业务需求需要使用splice对数组进行删减，然后发现在删除数据的时候，页面上显示的还是的删除前的数据。后来定位发现问题是因为key设置的不对从而导致的bug。

之前写过一篇diff算法的——[diff算法。
](https://caychance.github.io/posts/diff%E7%AE%97%E6%B3%95/#%E4%BB%80%E4%B9%88%E8%A6%81%E8%AE%BE%E7%BD%AEkey)

提到过为什么一定要设置key，设置了key就会复用。具有相同key的dom都会复用，这样子就会大大的提高性能。

但是平时设置的时候看到很多时候直接使用index的，这种情况下，不删除数据还好，一旦需要删除数据，就会有上述的问题出现。

之前在掘金上看到过一篇文章说有的大公司后台是需要专门给前端返回的list中加一个唯一的id让前端当做key来用。

如果没有这样的话，可以使用 `:key="index+JSON.stringify(item)"` 这样子来使用，可以保证key的唯一性。
