---
title: js event loop
date: 2018-12-06T15:45:40
toc: false
tags: 
  - js
  - event loop
---
----

JavaScript是单线程的，这与它的用途有关。

### 为什么JavaScript是单线程的？

假定JavaScript同时有两个线程，一个线程在某个DOM节点上添加内容，另一个线程删除了这个节点，这时浏览器应该以哪个线程为准？

### 任务分类

#### 同步任务和异步任务 

因为JavaScript是单线程的，如果加载一个图片，需要很久，那么网页就会阻塞。因此，从广义上来讲任务分为两类：

- 同步任务

>同步任务指的是，在主线程上排队执行的任务，只有前一个任务执行完毕，才能执行后一个任务

- 异步任务

>异步任务指的是，不进入主线程、而进入"任务队列"（task queue）的任务，只有"任务队列"通知主线程，某个异步任务可以执行了，该任务才会进入主线程执行。

执行顺序：

    （1）所有同步任务都在主线程上执行，形成一个执行栈。
    （2）主线程之外，还存在一个"任务队列"（task queue）。只要异步任务有了运行结果，就在"任务队列"之中放置一个事件。
    （3）一旦"执行栈"中的所有同步任务执行完毕，系统就会读取"任务队列"，看看里面有哪些事件。那些对应的异步任务，于是结束等待状态，进入执行栈，开始执行。
    （4）主线程不断重复上面的第三步。

![](https://blog-pics.pek3b.qingstor.com/006tKfTcly1g1jrpu6q3ij30g50boq3q.jpg)

#### 宏任务和微任务

对任务进行更精细的定义的话，也可以分为两类：

* 宏任务（macro-task）
    
>包括整体代码script、setTimeout、setInterval、setImmediate、requestAnimationFrame、I/O、UI Rendering

* 微任务（micro-task）

>new Promise().then(回调)、Process.nextTick、Object.observe、MutationObserver

### 浏览器中的Event Loop

执行顺序：

    1.当某个宏任务执行完后,会查看是否有微任务队列。
    2-1.如果有，先执行微任务队列中的所有任务，
    2-2.如果没有，会读取宏任务队列中排在最前的任务，
    3.执行宏任务的过程中，遇到微任务，依次加入微任务队列。
    4.栈空后，再次读取微任务队列里的任务，依次类推。
    
### Node中的Event Loop

![](https://blog-pics.pek3b.qingstor.com/006tKfTcly1g1js5cwo3wj30m808saad.jpg)

Node中的Event Loop不同于浏览器。Node运行机制如下：
    
* V8引擎解析JavaScript脚本。
* 解析后的代码，调用Node API。
* libuv库负责Node API的执行。它将不同的任务分配给不同的线程，形成一个Event Loop（事件循环），以异步的方式将任务的执行结果返回给V8引擎。
* V8引擎再将结果返回给用户。

其中libuv引擎中的事件循环分为6个阶段，它们会按照顺序反复运行。每当进入某一个阶段的时候，都会从对应的回调队列中取出函数去执行。当队列为空或者执行的回调函数数量到达系统设定的阈值，就会进入下一阶段。

![](https://blog-pics.pek3b.qingstor.com/006tKfTcly1g1js4x78vxj30lg0jm76o.jpg)

node中的事件循环的顺序：

外部输入数据-->轮询阶段(poll)-->检查阶段(check)-->关闭事件回调阶段(close callback)-->定时器检测阶段(timer)-->I/O事件回调阶段(I/O callbacks)-->闲置阶段(idle, prepare)-->轮询阶段（按照该顺序反复运行）

* timers 阶段：这个阶段执行timer（setTimeout、setInterval）的回调
* I/O callbacks阶段：处理一些上一轮循环中的少数未执行的 I/O 回调
* idle, prepare 阶段：仅node内部使用
* poll 阶段：获取新的I/O事件,适当的条件下node将阻塞在这里
* check 阶段：执行 setImmediate() 的回调
* close callbacks 阶段：执行 socket 的 close事件回调


### 区别

浏览器和Node 环境下，microtask 任务队列的执行时机不同

* 浏览器端，microtask 在事件循环的 macrotask 执行完之后执行
* Node端，microtask 在事件循环的各个阶段之间执行
* Node11版本中，Event Loop运行原理发生了变化，一旦执行一个阶段里的一个宏任务(setTimeout,setInterval和setImmediate)就立刻执行微任务队列，这点就跟浏览器端一致。

### 总结

浏览器端：
    
先执行宏任务，宏任务执行完以后，执行当前的微任务。然后再依次往后执行，循环。

Node端：

1.Node版本11 同浏览器端

2.Node版本10及以下

按照`poll=>check=>close callbacks=>timers=>I/O callbacks=>idle,prepare`的顺序执行，微任务会在每个阶段执行完以后执行。

