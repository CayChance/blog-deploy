---
title: "Socket"
date: 2019-07-15T20:25:14+08:00
draft: false
toc: false
images:
tags: 
  - socket
---
----

socket页面自动部署测试

之前在做业务的时候，有一种情况，前端需要写一个定时器，每个2秒去请求一遍后台的接口，这样子做其实很耗费性能的，现在计划改成使用socket的形式。

其实之前就简单了解一下WebSocket的基本概念，并没有深入的去做过。此处并不会记录一些基本的概念，而且介绍在实际的业务中，如何使用现成的工具去处理。

### sockjs-client和stompjs
sockjs-client和stompjs是两个现成的库，配合使用，可以帮助我们省很多事情。这样子的不好的地方也很明显，开发变得只是去使用现成的库。

``` js
/**
 * 背景：由于业务需求，多个模块需要每隔2s去轮询一下接口
 * 方案：使用socket来通讯，可避免前端耗费性能的去轮询接口
 */

import SockJS from "sockjs-client";
import Stomp from "stompjs";
import Bus from "./bus";


let socket = {};
let stompClient = null;
// 建立通讯地址
const DefaultConnectUrl = "http://xxx.com/endpointWisely";
const SubscribePath = "/topic/pod1"

/**
 * connect 建立连接
 */
socket.connect = () => {
  // 声明一个stomp客户端
  stompClient = Stomp.over(new SockJS(DefaultConnectUrl));
  // stomp客户端建立通讯
  stompClient.connect({}, frame => {
    // stomp订阅
    stompClient.subscribe(SubscribePath, response => {
      // 使用Bus.$emit将订阅到的数据分发出去
      Bus.$emit('socketData', response);
    })
  });
}

/**
 * send 主动给后台发送消息
 * @param { Function } sendPath 
 * @param { Object } param
 * @param { Object } config
 */
socket.send = (sendPath, param = {}, config = {}) => {
  if (!stompClient) return console.log('请先建立通讯');
  // stomp主动发送消息
  stompClient.send(sendPath, config, JSON.stringify(param));
}

/**
 * disconnect 断开连接
 */
socket.disconnect = () => {
  if (stompClient != null) {
    // stomp断开连接
    stompClient.disconnect();
    stompClient = null;
  }
}

export default socket;
```

### 参考链接
[WebSocket 教程](http://www.ruanyifeng.com/blog/2017/05/websocket.html)

[STOMP Over WebSocket](http://jmesnil.net/stomp-websocket/doc/)