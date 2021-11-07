---
title: Nodejs 使用 JWT 送 Cookie
tags:
  - nodejs
date: 2021-11-08 00:57:20
category:
---

Express 框架利用 Cookie 來送出 JWT，進行安全驗證。我們會利用 express 提供的 res.cookie 來進行實作

## 設定環境變數
打開 config.env 檔，這個檔案主要存放所有的環境變數，設定JWT_COOKIE_EXPIRES_IN，我們預設 Cookie 90天後過期

``` env
JWT_SECRET=sVNJpMqNcYwJ.lahq2343x4$234123^Ll!&H>+B{*]QS/i+zCw+y1>QRlMMf8Viiz2345+d
JWT_EXPIRES_IN=90d
JWT_COOKIE_EXPIRES_IN=90
```


## 設定 cookie

### 參數說明
res.cookie 的參數說明
- expire 
expires 表示 cookie 什麼時後過期，我們希望90天後過期，取得環境變數 process.env.JWT_COOKIE_EXPIRES_IN 之後，乘上 24 (轉換天數)，乘上 60(轉換成小時)，再乘上 60(轉換成分)，最後再乘 1000(轉換成毫秒)
``` js
expires: new Date(
  Date.now() + process.env.JWT_COOKIE_EXPIRES_IN * 24 * 60 * 60 * 1000
)
```
- secure
secure 在官方文件上解釋：`Marks the cookie to be used with HTTPS only.`，有設 定 secure 表示只含有 HTTPS 才會送出。當你的客戶端是 HTTP，沒有含有 SSL憑證時，cookie 便不會送出。
但在開發環境時通常不會設定 HTTPS，可以特別加上判斷，如果環境是production，才加上 `secure: true`

- httpOnly
httpOnly 官方文件解釋：`Flags the cookie to be accessible only by the web server.`，若設定 httpOnly: true，表示 cookie 標記只能由 Web Server 來訪問

官方範例
``` js
res.cookie('rememberme', '1', { 
  expires: new Date(Date.now() + 900000), 
  httpOnly: true 
})
```


### 完整程式碼
Express 提供 res.cookie 方法
`res.cookie(name, value [, options])`
我們設定傳給客戶端 jwt 的 cookie，值是 jwt 產生
- 返回的 cookie 名稱: jwt
- cookie jwt 值則是 jsonwebtoken 產生的 Token 值，帶入變數 token
- cookieOptions: 我們設定 expires, httpOnly 這兩個選項，並特別設定如果是 production 環境時才將 secure 設定為 true

``` js
const cookieOptions = {
  expires: new Date(
    Date.now() + process.env.JWT_COOKIE_EXPIRES_IN * 24 * 60 * 60 * 1000,
  ),
  httpOnly: true,
}

res.cookie('jwt', token, cookieOptions)
```
因此 API 除了返回 json 也會返回名叫 jwt 的 cookie ，並於 90 天後過期

``` js
const jwt = require('jsonwebtoken')
const signToken = (id) => jwt.sign({id}, process.env.JWT_SECRET, {
  expiresIn: process.env.JWT_EXPIRES_IN
})

const createSendToken = (user, statusCode, res) => {
  const token = signToken(user._id)
  const cookieOptions = {
    expires: new Date(
      Date.now() + process.env.JWT_COOKIE_EXPIRES_IN * 24 * 60 * 60 * 1000,
    ),
    httpOnly: true,
  }

  res.cookie('jwt', token, cookieOptions)

  // Remove password from output
  user.password = undefined

  res.status(statusCode).json({
    status: 1,
    token,
    data: {
      user,
    },
  })
}

exports.signup = catchAsync(async (req, res, next) => {
  const {
    name, email, password, passwordConfirm, passwordChangedAt,
  } = req.body

  const newUser = await User.create({
    name,
    email,
    password,
    passwordConfirm,
    passwordChangedAt,
  })

  createSendToken(newUser, 201, res)
})
```
## 測試
在 Postman 打註冊或登入 API，如果上述都有設定成功，預期成功的結果會返回 Cookie，如下圖所示
![Postman成功返回Cookie](../../../images/20211108/使用 JWT 送 Cookie.png)

## Reference
- [Express官方文件：res.cookie](https://expressjs.com/zh-tw/api.html)
- [[Udemy]Nodejs Express Bootcamp: Sending JWT via Cookie](https://www.udemy.com/course/nodejs-express-mongodb-bootcamp/learn/lecture/15065340?start=15#overview)