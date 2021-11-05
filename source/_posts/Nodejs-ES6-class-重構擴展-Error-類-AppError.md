---
title: '[Nodejs] ES6 class 重構擴展 Error 類 AppError'
date: 2021-10-14 22:21:10
tags:
- Nodejs
---
當 Error 發生時，通常會拋出一個 Error 物件，也可以看成是一個 Error 原型
筆記利用 ES6 語法 Class，建立一個 AppError 類，並繼承 Error，重構 app.js
![Github 完成程式碼](https://github.com/Hazelwu2/nodejs-practice/tree/17c5130980087e5ff31135fe345bcda303f9e12c)


### Class
對於 Class 並沒有很熟悉，這裡寫下 Class 基本概念，建議可以到阮一峰的 ES6 介紹文章，寫得很清楚完整

Class 定義：ES6 語法，實際上就是一個 function
ES6 Class 的寫法，在 ES5 裡也同樣可以做得到，所以常會聽到 class 是語法糖，它讓物件原型的寫法更清晰，更像物件程式設計的語法
Class 可用 extends 關鍵字來繼承，比起改 Javascript 原型鏈實現繼承，要更清晰也更方便

- constructor：建造方法，通過 new 生成實例時，會自動呼叫 constructor，每個類都一定會有 constructor，每個類都一定會有
- super：extends 繼承父類的 constructor 屬性與方法，像是 super.toValue() ，意思就是使用父層的 toValue() 方法
Class 侷限：不存在變數提升 Hoist，一定要先定義才能使用，若先使用再定義會報錯


#### appError 類
建立 utils/appError.js
``` js
class AppError extends Error {
  constructor(message, statusCode) {
    super(message)

    this.statusCode = statusCode
    this.status = `${statusCode}`.startsWith('4') ? 'fail' : 'error'
    this.isOperational = true

    Error.captureStackTrace(this, this.constructor)
  }
}

module.exports = AppError
```
class 使用到 extends， 繼承 Error 的原型

### Stack Trace

Stack Trace 堆疊追蹤
```
console.log(err.stack)
```
Stact 印出來的模樣
![Stack Trace 堆疊追蹤](../../../images/20211014/20211014nodejs-class.png)

`Error.captureStackTrace` 當 catch 到 error 時會取得 error.stack，便會調用此函數。
函數第一個參數要放 Object 物件，第二個參數則放選填的 function，它的作用是捕捉當前堆疊的路徑，並在傳入的物件裡（第一個參數）建立一個 stack 屬性來儲存，如果有傳入第二個參數，則會當成這次堆疊調用的最終站，程式只會跑到這個函數，也就是 this.constructor 被調用之前
可以用 console.log 印出來看看到底存入了什麼
``` js
Error.captureStackTrace(this, this.constructor)
console.log('stack', this.stack) // stack Error: Can't find /api/v1/asd on this server
```
最後打印出來 stack: 'Error: Can't find /api/v1/asd on this server'


#### 實際應用 new appError
回到 app.js，引用 AppError，並在 MiddleWare 攔截 * 路由時 new AppError 類
``` js
const express = require('express')

const AppError = require('./utils/appError')
const app = express()


app.use('*', (req, res, next) => {
  //* 以下是尚未重構前的代碼
  // res.status(404).json({
  //   status: 0,
  //   message: `Can't find ${req.originalUrl} on this server`,
  // })
  // err.status = 'fail'
  // err.statusCode = 404

  // 重構後的代碼
  next(new AppError(`Can't find ${req.originalUrl} on this server`), 404))
})
```
接下來重構這段程式碼，當遇到錯誤時 json 吐回訊息
``` js
app.use((err, req, res, next) => {
  // Stack Trace 堆疊追蹤
  console.log(err.stack)

  err.statusCode = err.statusCode || 500
  err.status = err.status || 'error'

  // 回傳錯誤訊息
  res.status(err.statusCode).json({
    status: err.status,
    message: err.message,
  })
})
```
新增檔案 controllers/errorController.js
``` js
module.exports = (err, req, res, next) => {
  // Stack Trace 堆疊追蹤
  console.log(err.stack)

  err.statusCode = err.statusCode || 500
  err.status = err.status || 'error'

  // 回傳錯誤訊息
  res.status(err.statusCode).json({
    status: err.status,
    message: err.message,
  })
}
```

回到 app.js，將內容刪除，並引入 errorController，再將原本的程式碼刪除，替代為 globalErrorHandler，將 app.js 精簡很多
``` js
const globalErrorHandler = require('./controllers/errorController)

app.use(globalErrorHandler)
```

### Reference
- [ES6-Class的繼承](https://yucj.gitbooks.io/ecmascript-6/content/docs/class.html)
- [掌握 JS Stack Trace](http://jartto.wang/2017/12/09/grasp-js-stack-trace/)
- [Creating Custom Error Objects In Node.js With Error.captureStackTrace()](https://www.bennadel.com/blog/2828-creating-custom-error-objects-in-node-js-with-error-capturestacktrace.htm)