---
title: "PostMessage使用"
date: 2019-07-11T16:47:15+08:00
draft: false
toc: false
images:
tags: 
  - 跨域
  - postMessage
---
----

跨域的问题有一种postMessage的解决方案，之前也一直是听过但是没有用过。

### 用法

- 发送消息:otherWindow.postMessage(message, targetOrigin, [transfer]);
- 接收消息:window.addEventListener("message", (event)=>{}, false);
- postMessage方法的入参:
  - message:要发送的数据
  - targetOrigin:数据接收方。设置为'*'，则不限制。不建议这么设置。
  - transfer:可选参数。是一串和message 同时传递的 Transferable 对象.这些对象的所有权将被转移给消息的接收方，而发送一方将不再保有所有权。
- event对象的几个属性:
  - data:从其他窗口发送过来的消息对象
  - type:发型消息的类型
  - source:发送消息的窗口对象
  - origin:发送消息的窗口的源

### 举个栗子🌰

下面的写了一个小demo，page one (http://10.161.114.80:8080/index.html/#/test) 和page two (http://10.161.114.80:8082/index.html/#/test)。

1. page one在mounted的时候，新打开了一个窗口，然后8秒后了一条消息给page two；
2. page one在mounted的时候，监听postMessage回来的消息；并判断如果当前发消息的窗口对象，不是当前窗口对象(window)的话，就发送一条消息给page two。
3. page two在mounted的时候，监听postMessage回来的消息。
4. page two在mounted的时候，发送一个消息给page one。



``` js
//page one
<template>
  <div>
    <h3>page one</h3>
    <input v-model="message" placeholder="edit me" />
    <button @click="handleClick">sendMessage</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      message: "",
      popup: null
    };
  },
  methods: {
    /**
     * 打开新窗口
     */
    openWindow() {
      this.popup = window.open(
        "http://10.161.114.80:8082/index.html/#/test",
        "title"
      );
      setTimeout(()=>{
        this.sendMess("Hello World!");
      },8000)
    },

    /**
     * 使用postMessage发送消息
     */
    sendMess(message) {
      this.popup.postMessage(
        message,
        "http://10.161.114.80:8082/index.html/#/test"
      );
    },

    /**
     * 点击按钮 发送消息
     */
    handleClick() {
      if (!this.message) return;
      this.sendMess(this.message);
    },

    /**
     * 接收消息
     */
    receiveMess() {
      window.addEventListener(
        "message",
        function(e) {
          console.log(e);
          if (e.source != window) {
            // 如果当前source不是window(当前窗口)的话，再发送一条消息出去
            e.source.postMessage(
              "hello cc",
              "http://10.161.114.80:8082/index.html/#/test"
            );
          }
        },
        false
      );
    }
  },
  mounted() {
    this.openWindow();
    this.receiveMess();
  }
};
</script>

//page two 
<template>
  <div>
    <h3>page two</h3>
    <input v-model="message" placeholder="edit me" />
    <button @click="handleClick">sendMessage</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      message: ""
    };
  },
  methods: {
    /**
     * 点击按钮 发送消息
     */
    handleClick(message) {
      if (!this.message) return;
      this.sendMess(this.message);
    },

    /**
     * 使用postMessage发送消息
     */
    sendMess(message) {
      window.opener.postMessage(
        message,
        "http://10.161.114.80:8080/index.html/#/test"
      );
    },

    /**
     * 接收消息
     */
    receive() {
      window.addEventListener(
        "message",
        function(e) {
          console.log(e.data);
        },
        false
      );
    }
  },
  mounted() {
    this.receive();
    this.sendMess("Nice to see you");
  }
};
</script>
```

### postMessage的应用场景

最近在看之前同事留下来的代码，其中有一个功能大量用到postMessage。

postMessage常用于两个页面之间通讯。或者页面中引入了iframe，想让当前页面和iframe窗口页面进行通讯。可以使用postMessage。应用场景就是，一般不依赖后台服务，就单纯的前端页面之间的交互。

### !!!注意事项

- 直接在浏览器中打开A和B两个页面，之间是无法使用postMessage通讯的。符合通讯的有两种情况：
- 情况一：必须其中一个页面是另一个页面通过window.open()打开的；
- 情况二：A、B两个页面是页面嵌套iframe的关系。可以在iframe的load事件回调中发送请求。
- 监听message事件的时候需要对event.origin进行过滤。