---
title: MongoDB基础入门
date: 2019-02-01T10:23:29
toc: false
tags: 
  - mongodb
  - 数据库
---
-----

## MongoDB基础入门（基础篇）

最新心血来潮，准备在网上学习一下Vue+Node+MongoDB。之前接触过MySQL，但是MongoDB跟MySQL还是有区别的。话不多说：

### 基本概念
| SQL术语/概念 | MongoDB术语/概念 | 解释/说明 |
| ------ | ------ | ------ |
| database | database | 数据库 |
| table | collection | 数据库表/集合 |
| row	| document | 数据记录行/文档 |
| column	| field	| 数据字段/域 |
| index |	index |	索引 |
| table joins	|  | 表连接,MongoDB不支持 |
| primary key |	primary key |	主键,MongoDB自动将_id字段设置为主键 |

### 数据库
1. 一个mongodb中可以建立多个数据库。

2. 每个数据库里面有多个表（也叫做集合）。

3. 表里面存储的数据格式就是一个json，这也是区别于SQL的地方。

### 下载安装 
这一步就省略了，自己Google去吧。我在mac下用的homebrew

### 连接数据库
在命令行中输入mongo就会连接到数据库

### 数据库相关
`show dbs`           查看所有数据库

`use DATABASE_NAME`  创建数据库或者切换到该数据库

`db.dropDatabase()`  删除数据库

### 集合相关
`show tables`                           查看已有表

`show collections`                      查看已有集合

`db.createCollection(name, options)`    创建集合

`db.COLLECTION_NAME.drop()`             删除集合

**PS：show tables 和 show collections 的区别，网上还么有搜到，但是实际操作了一下，发现两者是一样的。**

直接在COLLECTION_NAME集合中插入数据的时候，会自动创建COLLECTION_NAME集合
`db.COLLECTION_NAME.insert({"name" : "Chance"}) `

**情况1：如果没有新建一个数据库的话，会默认在test数据库中新建一个COLLECTION_NAME集合（表）**

**情况2：在该命令之前新建了一个数据库的话，会在该数据库下新建一个COLLECTION_NAME集合（表）**

### 增删改查

增删改查的操作是基于集合来的，所有统一的格式都是db.COLLECTION_NAME.***

增
```
db.COLLECTION_NAME.insert(document)
```

```
db.COLLECTION_NAME.save(document) 
```

删
```
db.COLLECTION_NAME.remove(
   <query>,
   {
     justOne: <boolean>,
     writeConcern: <document>
   }
)
```

改
```
db.COLLECTION_NAME.update(
   <query>,
   <update>,
   {
     upsert: <boolean>,
     multi: <boolean>,
     writeConcern: <document>
   }
)
```

```
db.COLLECTION_NAME.save(
   <document>,
   {
     writeConcern: <document>
   }
)
```

查
```
db.COLLECTION_NAME.find(query, projection)
```

### 常用命令
| 命令 | 解释/说明 |
| ------ | ------ |
| show dbs | 查看所有数据库 |
| db | 显示当前数据库对象或集合 |
| use DATABASE_NAME | 如果数据库不存在，则创建数据库，否则切换到指定数据库。 |
| show tables | 查看该数据库下的所有表 |
| show collections | 查看该数据库下的所有集合 |
| db.dropDatabase() | 删除数据库 |
| db.COLLECTION_NAME.insert({"name":"Chance"}) | 创建集合并向其中插入一些数据 |
| db.createCollection(name, options) | 创建集合 |
| db.COLLECTION_NAME.drop() | 删除当前集合 |
| db.COLLECTION_NAME.insert(document) | 增 |
| db.COLLECTION_NAME.remove() | 删 |
| db.COLLECTION_NAME.update() | 改 |
| db.COLLECTION_NAME.insert(document) | 查 |

### 写在最后
正常情况下，node中不会直接使用mongo的语法，会使用mongoose

[参考链接-mongoose官网](https://mongoosejs.com/)

[参考链接-mongoose教程](https://www.bilibili.com/video/av41033371)