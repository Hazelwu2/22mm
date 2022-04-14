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