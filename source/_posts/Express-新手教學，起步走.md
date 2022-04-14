---
title: Express 新手教學，起步走
date: 2022-04-15 03:27:15
tags:
- Nodejs
- Express
category:
- Express
---


# Nodejs Express 框架
Nodejs Express 屬於輕量型框架，本篇文章寫架設 Express 起步走的新手教學

## Express
安裝起步走
``` bash
$ mkdir express-practice && cd express-practice
$ npm init
$ npm install express --save
```

### 設定 Web Server
建立 app.js

``` js
const express = require('express)
const app = express()

app.get('/', (req, res) => {
  res.send('Test Express')
  // res.send('<html><h1>Hi</h1></html>')
})

// 監聽 Port
const PORT = 10998
app.listen(PORT)
```

執行 app.js 啟動 Web 伺服器

```
node app.js
```

### 網址規則

`https://wualnz.com/search?q=express`
- http：傳輸協定，通常是 https，才會出現綠色安全鎖頭
- wualnz： 主網域，由域名供應商提供
- 子網域：www.wualnz.com, email.wualnz.com
- ?q=express：Query 參數，解析出來會是 `{q: 'express'}`


#### HTTP 協定
GET
``` js
app.get('/', (req,res) => res.send('GET Response'))
```
POST
``` js
app.post('/', (req,res) => res.send('GET Response'))
```

可以將 get, post 替代成以下 HTTP 方法
get、post、put、head、delete、options、 trace、copy、lock、mkcol、move、purge、propfind、proppatch、unlock、report、mkactivity、checkout、merge、m-search、notify、subscribe、unsubscribe、patch、search，以及 connect

#### Query 參數
開頭會有 ?，每個參數都會有 & 的符號做連接
`example.com/search?q=hello`
`text=hello`
`text=hello&today=coolman`

```
{
  text: 'hello',
  today: 'coolman'
}
```

### 靜態路由
靜態路由，網址輸入 /user/profile 會到「瀏覽個人檔案」、 /user/edit-profile，會到「編輯個人檔案」
``` js
app.get('/user/profile', (req, res) => {
  res.send('瀏覽個人檔案')
})
app.get('/user/edit-profile', (req, res) => {
  res.send('編輯個人檔案')
})
```

### 動態路由
`wualnz.com/user/hazel`，取得 hazel 動態的名字，不同使用者會輸入不同的名字，可以藉由 req.params 取得指定路徑

路由設定 `:name`，再用 `req.params.name` 去接到參數

``` js
app.get('/user/:name', (req, res) => {
  res.send(req.params.name)
})
```

### query 取得網址參數
`wualnz.com/search?q=express`
抓取網址列上的參數 `q=express` 資料

``` js
app.get('/search', (req, res) => {
  const search = req.query.q 
  console.log(search) // express

  res.send(search)
})
```

### Router 進階
新增 routes 資料夾，並在 routes資料夾建立 user.js
目錄結構
- routes
  - user.js

user.js
``` js
const express = require('express')
const router = express.Router()

router.get('/user/profile', (req, res) => {
  res.send('Profile)
})

module.exports = router
```

app.js 讀取 user router
``` js
const express = require('express')
const app = express()

const user = require('./router/user')
app.use('/user', user)
```
此方法可以大幅降低 app.js 肥沃的程度，也會變得相當乾淨、簡潔

## 常用工具

### express-generator
快速建立應用程式的架構
```
$ npm install express-generator
```

-e --ejs: 產生 ejs template
-e --hbs: 產生 handlebars template
-e --pug: 產生 pug tempalte

``` bash
$ express --view=pug myapp
$ express -e project
$ cd prject && npm install
$ npm start
```
