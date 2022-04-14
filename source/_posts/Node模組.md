---
title: Nodejs模組
date: 2022-04-14 22:30:00
tags:
category:
- Nodejs
---


## Path
載入 Path 模組
``` js
const path = require('path')
// 抓目錄路徑
path.dirname('/xx/yy/zz.js') // 回傳 /xx/yy
// 抓檔案名稱
path.basename('sideproject/test/test.js') // test.js
// 抓副檔案
console.log(path.extname('sideproject/test/test.js')) // .js
// 分析路徑
console.log(path.parse('sideproject/test/test.js'))
// { root: '/', dir: '/sideprojecct/test', base: 'test.js', ext: '.js', name: 'test' }

// 路徑合併
path.join(__dirname,'/xx')
```

## http 模組
載入 Http 模組

``` js
const http = require("http");
const PORT = process.env.PORT || 10888

http.createServer((req, res) => {
  res.writeHead(200, {
    "Content-Type": "text/plain"
  })
  res.write("Hello")
  res.end()
})

const server = http.createServer(app)
server.listen(PORT)
```

[使用 Http request 取得 body data 方式](https://nodejs.dev/learn/get-http-request-body-data-using-nodejs)

``` js
const server = http.createServer((req, res) => {
  // we can access HTTP headers
  req.on('data', chunk => {
    console.log(`Data chunk available: ${chunk}`)
  })
  req.on('end', () => {
    //end of data
  })
})
```
可改寫成 Promise 版本
``` js
const server = http.createServer(async (req, res) => {
  const buffers = [];

  for await (const chunk of req) {
    buffers.push(chunk);
  }

  const data = Buffer.concat(buffers).toString();

  console.log(JSON.parse(data).todo); // 'Buy the milk'
  res.end();
})
```