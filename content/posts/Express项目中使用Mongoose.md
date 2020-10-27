---
title: "Express项目中使用Mongoose"
date: 2020-10-27T10:52:28+08:00
draft: false
toc: false
images:
tags: 
  - node
  - express
  - 数据库
  - mongoose
---

### 安装MongoDB

安装以及启动mongodb遇到好几个问题。

首先，不能使用brew安装了，因为MongoDB不再开源了。

后来就按照菜鸟教程上的安装方法进行安装：
```
# 进入 /usr/local
cd /usr/local

# 下载
sudo curl -O https://fastdl.mongodb.org/osx/mongodb-osx-ssl-x86_64-4.0.9.tgz

# 解压
sudo tar -zxvf mongodb-osx-ssl-x86_64-4.0.9.tgz

# 重命名为 mongodb 目录

sudo mv mongodb-osx-x86_64-4.0.9/ mongodb
```

[菜鸟教程](https://www.runoob.com/mongodb/mongodb-osx-install.html)

### 启动MongoDB

为MongoDB新建默认目录的时候执行命令如下
```
sudo mkdir -p /data/db
```

结果报错，后来网上查了一下发现<b>最新版的Mac系统：不允许更改根目录</b>

后来就在mac上新建了一个非根目录的目录当做MongoDB的默认目录。当然需要确保```~/data/db```目录存在。
```
mongod --dbpath ~/data/db
```

[参考地址](https://www.cnblogs.com/nayek/p/12221379.html)


### 生成express应用

可以express-generator来快速生成express应用，当然首先需要安装好express-generator。

```
express mongooseDemo
```

[暖风博客园](https://www.cnblogs.com/woodk/p/6155955.html)
