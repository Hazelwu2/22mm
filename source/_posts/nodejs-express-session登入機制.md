---
title: nodejs-express-session登入機制
date: 2022-02-01 20:46:15
tags:
category:
- express
- expresss-session
---

## 安裝
通常 exress 實作登入機制時都會使用知名的 express-session，按照下列指令來安裝套件
``` bash
npm i express-session --save
```

## 設定 express-session
在 express 專案的 app.js 引入 `express-session`

```
const session = require('express-session')
```

並且用 `app.use` 來設定初始值

express-session 設定說明
Cookie 存的是 session id ，而 session data 則存在 server

- secret：必填選項，存放在 cookie 的 session ID
- resave：每一次與使用者互動，是否強制保存 session 並更新到 session store 裡，預設是 true
- saveUninitialized強制將未初始化的 session 存回 session store。未初始化表示這個 session 是新的而且沒有被修改過，例如未登入的使用者的 session。設定 false 可避免存入太多空的 session

``` js
// Cookie 存的是 session id，session data 存 server
app.use(session({
  secret: 'K,_r*p3h~7G}hq]iL~#jIe7|DMB?E', // 存放在 cookie 的 session id
  resave: false, // 是否強制保存 session，預設是 true
  saveUninitialized: true // session 還沒修改前是否存入 cookie，設定 false 可避免存入太多空的值
}))

```
