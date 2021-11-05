---
title: '[Nodejs] Express 使用者註冊利用 JWT(jsonwebtoke)驗證'
date: 2021-10-18 13:43:29
tags:
- nodejs
---
Nodejs 用 Express 框架，使用者註冊 API，利用 JWT 產生令牌驗證使用者

## 安裝 jsonwebtoken
```
npm i jsonwebtoken
npm run start
```

## 使用 JWT
在 authController.js 在 signup 時引用 jwt

``` js
const jwt = require('jsonwebtoken')
```
### 設定 JWT 環境變數
我們在環境變數上新增 JWT_SECRET、JWT_EXPIRES_IN
``` env
JWT_SECRET=sVNJpMqN34i5$JkSK4E%#255CK#sV3
JWT_EXPIRES_IN=90d
```
JWT_EXPIRES_IN 90d 意味者 90天後過期，90天後 JWT 不再會有效，即使簽名是正確的，是一種額外的安全措施。
接著在回到 authController.js，利用 jwt.sign 方法，引用剛剛建立的 JWT 環境變數，來產生令牌

### 設定 token
``` js
const token = jwt.sign({ id: newUser._id }, process.env.JWT_SECRET, {
  expiresIn: process.env.JWT_EXPIRES_IN,
})
```
若令牌正確，回傳給客戶端令牌，Client端會以 cookie/storage 儲存令牌(token)

### 回傳 Client端 token
``` js
const token = jwt.sign({ id: newUser._id }, process.env.JWT_SECRET, {
  expiresIn: process.env.JWT_EXPIRES_IN,
})

res.status(201).json({
  status: 1,
  token,
  data: {
    user: newUser
  }
})
```

``` js
const jwt = require('jsonwebtoken')
const User = require('../models/userModel')
const catchAsync = require('../utils/catchAsync')

exports.signup = catchAsync(async (req, res, next) => {
  const { name, email, password, passwordConfirm}

})
```

## 測試
設定好之後，我們用 postman 打 /signup API，如果設定都正確，預期結果會回傳 JWT 產生的 token
![jwt success](../../../images/20211018/token.png)

## JWT Debugger
Token 部分是由 Base64 編碼，我們可以在 [jwt.io](jwt.io) 調試我們產生的 JWT Token，轉換回原本的 JSON 資料，同時也可以驗證該 Token 的正確性

![JWT IO Screen Shot](../../../images/20211018/jwtio.png)


## Reference
- [[筆記] 透過 JWT 實作驗證機制](https://medium.com/%E9%BA%A5%E5%85%8B%E7%9A%84%E5%8D%8A%E8%B7%AF%E5%87%BA%E5%AE%B6%E7%AD%86%E8%A8%98/%E7%AD%86%E8%A8%98-%E9%80%8F%E9%81%8E-jwt-%E5%AF%A6%E4%BD%9C%E9%A9%97%E8%AD%89%E6%A9%9F%E5%88%B6-2e64d72594f8)
- [[Udemy] Nodejs Express Mongodb Bootcamp - 註冊用戶](https://www.udemy.com/course/nodejs-express-mongodb-bootcamp/learn/lecture/15065210)