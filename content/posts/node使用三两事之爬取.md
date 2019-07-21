---
title: nodejs使用三两事之爬取
date: 2019-05-29T23:48:31+08:00
draft: false
toc: false
images:
tags: 
  - node
---

----

遇到这么一个问题，女朋友负责做某个汽车品牌的公关活动，平时每隔一段时间会有人发一些关于该品牌的一些文章，需要她们逐行去看并且得出一个结论，这篇文章是夸赞的，还是批评的？做好分类之后再发送给下一个环节。


- 需求
输入一堆的url，根据不同的url来做不同的爬取规则。抓取到链接中的所有内容后，使用AI去分析当前段落或者文章的情感度，正面还是负面。（此例子中均用微信公众号中的链接去做抓取。）

- 拆分
  - step1: 把所有的url以数组的形式存在json中。其实也可以考虑做一个简单的页面，把所有的url以xml表格的形式上传并且读取。
  - step2: 确定爬取的规则和爬取的工具，自动爬取到数据并保存到本地。puppeteer
  - step3: 确定分析段落或者文章的工具，自动来分析当前文章的情感度。腾讯的一个工具，收费的。

``` js
// index.js
const puppeteer = require('puppeteer');
const {fileWrite} = require('./file-write');
const fs = require('fs');

/**
 * readFile 把node的fs.readFile封装成promise形式
 * @param {String} url 
 */
const readFile = function (url) {
  return new Promise((resolve, reject) => {
    fs.readFile(url, 'utf-8', (err, data) => {
      if (err) reject(err);
      resolve(data);
    })
  })
};

(async () => {
  const browser = await puppeteer.launch({ headless: false })
  const page = await browser.newPage()
  const urlList = JSON.parse(await readFile('./url.json'))
  let temArr = [];
  for(let {url,title,author} of urlList){
    await page.goto(url)
    const pageInnerText = await page.evaluate(() => {
      //浏览器中执行
      const pageDom = document.querySelector('#img-content');
      if (!pageDom.innerText) retutn ;
      return {
        innerText: pageDom.innerText,
        documentTitle: document.title,
      };
    })
    temArr.push(Object.assign(pageInnerText,{url,title,author}))
  }
  fileWrite(temArr)
  await browser.close()
})()
```

``` js
// file-write.js
const fs = require('fs');

/**
 * fileWrite 把爬取到的数据追加到message.json文件里面
 * @param {Array} arr 
 * @param {String} path 
 */
function fileWrite(arr=[],path = './message.json'){
  fs.readFile(path, 'utf8', (err, data) => {
    let tem;
    if (err && err.code === 'ENOENT') tem = [];
    else {
      tem = data?JSON.parse(data):[];
    }
    tem = arr;
    fs.writeFile('message.json', JSON.stringify(tem), (err) => {
      if (err) throw err;
      console.log('数据已追加到文件');
    });
  });
}

module.exports = {fileWrite};
```
其实step3这一块很深，然后本身精力有限所以也只能使用一些现有的工具之类，当然感兴趣的朋友可以自己来做这一块。