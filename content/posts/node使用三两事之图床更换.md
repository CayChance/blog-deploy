---
title: node使用三两事之图床更换
date: 2019-05-24T16:34:15
toc: false
tags: 
  - node
---

----

前几天微博可能是官方在采取防盗链措施，导致很多使用微博图床做博客的童鞋的图片全部都403了。所以准备换一个，之前注册了七牛云，但是没有实名认证，所以去实名认证了一下，结果，我把身份证正反面上传了，但是一直提示我上传失败，具体原因也没有，F12打开开发者模式，也看不到接口报错的信息。手机上试了2次，电脑上试了3次，然后放弃了。最后注册了[青云。](https://console.qingcloud.com/)话说青云的服务还是挺不错的，希望可以一直坚持一下去，我注册了以后，就及时的打电话跟我沟通。

### 博客里面的图片需要手动替换吗
博客里面的图片是个烦心事，总不能手动去替换吧？

- 需求：
使用node写一个脚本，替换掉blog中的所有新浪图床的图片链接。

- 拆分：
  - step1: 获取blog中的所有新浪图床图片的链接，并且保存起来data.json。
  - step2: 读取data.json文件，并把所有的图片保存在同级目录下的pics下。
  - step3: 使用青云的API自动上传pics下的所有图片。
  - step4: 使用新链接自动替换掉老连接。

step1
``` js
/**
 * Created by chenchen on 2019/5/22.
 */

let fs = require('fs');
let path = require('path');
let dirList = [];
let result = {};
const  _postsPath = path.resolve(__dirname,'./../source/_posts');

/**
 * promiseMethod 把node的异步方法转成promise形式
 * @param {String} methodName 要修改的方法名
 * @param {String} path  路径
 * @param {any} param  请求配置
 * @returns Promise
 */
const promiseMethod = function (methodName) {
  return function (path,param) {
    return new Promise((resolve,reject)=>{
      methodName(path,param?param:'utf-8',(err,data)=>{
        if(err) reject(err);
        else resolve(data)
      })
    })
  }
}

const readDirByPromise = promiseMethod(fs.readdir);
const readFileByPromise = promiseMethod(fs.readFile);
const writeFileByPromise = promiseMethod(fs.writeFile);

//匹配出posts目录下的所有新浪图床的链接，并保存到data.json
async function getUrl() {
  let tempArr = [];
  dirList = await readDirByPromise(_postsPath);
  dirList.forEach(async function (item,index) {
    let data = await readFileByPromise(`${_postsPath}/${item}`);
    let tempResult = data.match(/https:\/\/blog\-pics[\S]*jpg/g);
    if(tempResult instanceof Array){
      tempArr = tempArr.concat(tempResult);
    }
    if(dirList.length-1 === index){
      result = [...new Set(tempArr)];
      writeFileByPromise('./data.json',JSON.stringify(result));
    }
  })
}

getUrl();
```

step2
``` js
/**
 * Created by chenchen on 2019/5/23.
 */

let fs = require('fs');
let request = require('request');

const promiseMethod = function (methodName) {
  return function (path,param) {
    return new Promise((resolve,reject)=>{
      methodName(path,param?param:'utf-8',(err,data)=>{
        if(err) reject(err);
        else resolve(data)
      })
    })
  }
}

/**
 * downLoadPic 下载图片到本地
 * @param {String} url 下载图片的地址
 * @param {String} filename  图片保存的名字
 * @param {Function} callback  回调函数
 * @returns null
 */
const downLoadPic = function (url, filename, callback) {
  request.head(url, function(err, res, body){
    console.log('content-type:', res.headers['content-type']);
    console.log('content-length:', res.headers['content-length']);
    request(url).pipe(fs.createWriteStream(filename)).on('close', callback);
  });
}

const readFile = promiseMethod(fs.readFile);

// 读取step1中data.json中的url，然后依次下载保存。
async function getData() {
  let urlList = await readFile('./data.json');
  urlList = JSON.parse(urlList);
  urlList.forEach((item,index)=>{
    let picName = item.match(/https:\/\/ws[0-9].sinaimg.cn\/large\/(.+).jpg/)[1];
    downLoadPic(item,`./pics/${picName}.jpg`,function () {});
  })
}

getData();

// downLoadPic('https://blog-pics.pek3b.qingstor.com/006tNbRwly1fxq5n1iao5j30go097gm9.jpg','./test.jpg',function () {
//   console.log('down');
// });
```

### 分享一个面试题
使用node遍历某个目录下的所有文件

``` js
let fs = require('fs');
let result = [];

// 方法1：同步方法读取
function readDirSync(path){
  let paths = fs.readdirSync(path);
  paths.forEach(item=>{
    let info = fs.statSync(`${path}/${item}`);
    if(info.isDirectory()){
      console.log('dir',item);
      readDirSync(`${path}/${item}`);
    }
    else{
      console.log('file',item);
      result.push(item);
    }
  })
}
readDirSync(__dirname);

// 方法2：异步方法读取
function readDir(path){
  fs.readdir(path,function(err,dirs){
    if(!dirs) return ;
    dirs.forEach(item=>{
      fs.stat(`${path}/${item}`,function(err,info){
        if(info.isDirectory()){
          console.log('dir',item);
          readDir(`${path}/${item}`)
        }
        else{
          console.log('file',item);
          result.push(item);
        }
      })
    })
  })
}
readDir(__dirname);


```
