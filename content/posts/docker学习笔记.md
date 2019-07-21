---
title: docker学习笔记
date: 2019-03-06T19:26:50
toc: false
tags: 
  - 工具
  - docker
---
----

### 引子-环境配置的难题

软件开发最大的麻烦事之一就是环境配置。解决的思路就是软件带环境安装，即安装的时候，把原始环境一模一样的复制过来。

### 初始的尝试解决方案-虚拟机

虚拟机就是带环境安装的一种解决方案。

缺点:

1. 资源占用多
虚拟机会独占一部分内存和硬盘空间。

2. 冗余步骤多
虚拟机是完整的操作系统，一些系统级别的操作步骤，无法跳过。

3. 启动慢
启动虚拟机的时间和启动操作系统的时间一样长。

### 另一种解决方案-Linux容器

由于以上缺点，Linux有另一种虚拟化技术:Linux容器。

**Linux容器不是模拟一个完整的操作系统，而且对进程进行隔离。**

或者说在正常的进程外面套了一个保护层。对于容器里面的进程来说，它接触到的各种资源都是虚拟的。

由于容器是进程级别的，相比虚拟机有以下优势:

1. 启动快
  <br>启动进程肯定比启动操作系统快太多了。

2. 资源占用少
  <br>容器只占用需要的资源。另外，多个容器可以共享资源。

3. 体积小
  <br>容器只要包含用到的组件即可。

结论:容器有点类似轻量级的虚拟机，能够提供虚拟化的环境，但是成本开销小得多。

### 噔噔噔～主角登场-Docker是个啥

**Docker属于Linux容器的一种封装，提供简单易用的容器使用接口。**

Docker将应用程序与该程序的依赖，打包在一个文件里面。运行这个文件，就会生成一个虚拟容器。程序在这个虚拟容器里运行，就好像在真实的物理机上运行一样。(有了 Docker，妈妈再也不用担心环境问题。)

此外，容器还可以进行版本管理、复制、分享、修改，类似代码的管理。

### Docker的用途

Docker 的主要用途，目前有三大类。

1. 提供一次性的环境。
  <br>比如，本地测试他人的软件、持续集成的时候提供单元测试和构建的环境。

2. 提供弹性的云服务。
  <br>因为 Docker 容器可以随开随关，很适合动态扩容和缩容。

3. 组建微服务架构。
  <br>通过多个容器，一台机器可以跑多个服务，因此在本机就可以模拟出微服务架构。

### 咦？Docker中的image是个啥

**Docker把应用程序及其依赖，打包在image文件里面。**

我们通过image文件，可以生成Docker容器。

image文件可以看作是容器的模板。Docker根据image文件生成容器的实例。同一个image文件，可以生成多个同时运行的容器实例。

image是二进制文件。image文件可以继承别的image文件，一般来讲，尽量使用别人制作好的docker，或者基于别人的image文件进行加工。

### 容器文件又是个啥？

image文件能够生成的容器实例，这个容器实例本身也是一个文件，称为容器文件。

所以说，一旦容器生成，会同时有两个文件:image文件和容器文件。

关闭容器，并不会删除容器文件，只是容器停止运行而已。

### 我也想做一个Docker容器，肿么办？

step1: 编写Dockerfile
在项目的根目录下，新建一个文本文件.dockerignore和Dockerfile。前者是docker打包要忽略的路径;后者是docker打包的代码
``` docker
FROM node:8.4
COPY . /app
WORKDIR /app
RUN npm install --registry=https://registry.npm.taobao.org
EXPOSE 3000
```
step2: 创建image文件
在dockerfile文件的基础上使用docker image build 命令创建image文件。

step3: 生成容器
docker container run 命令会从image文件生成容器。

### docker常用命令

#### 版本相关
```
docker version //docker客户端和服务端的版本号
docker info //docker更加详尽的信息
```

#### image相关
``` 
docker image ls //列出所有的docker image
docker image rm [imageId] //删除指定image
docker image build -t koa-demo . //创建 image 文件
docker image prune //清除所有image
```

#### 运行一个image并启动容器
```
docker image pull library/hello-world //将image文件从仓库抓取到本地
docker container run hello-world //运行image文件生成容器，每运行一次，就会新建一个容器。
docker container start [containerID] //重复使用容器
docker container kill [containID] //终止指定容器运行  相当于向容器里面的主进程发出 SIGKILL 信号
docker container stop [containID] //终止指定容器运行  相当于向容器里面的主进程发出 SIGTERM 信号
这两个信号的差别是，应用程序收到 SIGTERM 信号以后，可以自行进行收尾清理工作，但也可以不理会这个信号。如果收到 SIGKILL 信号，就会强行立即终止，那些正在进行中的操作会全部丢失。
```

#### container相关
```
docker container ls //列出正在运行的容器
docker container ls -a //列出所有的容器，包括终止运行的容器
docker container kill [containID] //终止指定容器运行
docker container rm [containerID] //删除指定容器文件
docker container prune //清除所有container
docker system prune //清除所有
```

#### 发布image
```
docker login //登录
docker image tag [imageName] [username]/[repository]:[tag] // 为本地的 image 标注用户名和版本。docker image tag koa-demos:0.0.1 cc/koa-demos:0.0.1
docker image build -t [username]/[repository]:[tag] . //重新构建image文件
docker image push [username]/[repository]:[tag] //发布image文件
```

以上都是简单介绍。

[参考链接-阮老师的教程](http://www.ruanyifeng.com/blog/2018/02/docker-tutorial.html)
