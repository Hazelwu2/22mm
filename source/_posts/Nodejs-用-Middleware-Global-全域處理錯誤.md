---
title: '[Nodejs] 用 Middleware Global 全域處理錯誤'
date: 2021-10-09 16:11:30
tags:
---

在 Nodejs Express 中用 Middleware 錯誤處理
官方提供錯誤處理的函數，參數有 err, req, res, next。
我們可以使用 app.use 方式來呼叫

``` js
app.use((err, req, res, next) => {
  console.error('有錯誤')
  res.status(500).json({
    status: 500
    message: 'Ohhh, 發生錯誤'
  })
})
```


app.js
``` js

app.all('*', (req, res, next) => {
  const err = new Error(`Ohhhh, cannot find this ${req.originalUrl}`)
  err.status = 'fail'
  err.statusCode = 404

  next(err)
})

app.use((err, req, res, next) => {
  err.statusCode = err.statusCode || 500
  err.status = err.status || 'error'

  res.status(err.statusCode).json({
    status: err.status,
    message: err.message
  })
})
```

### 成果
輸入不存在的 routes 時便會出現錯誤訊息
![最後呈現的結果](/images/202111009-nodejs-error-handle.png)

### Reference
- [Express Error Handle](https://expressjs.com/zh-tw/guide/error-handling.html)