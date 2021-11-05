---
title: '[Nodejs] 重構 Controller try catch'
date: 2021-10-14 23:42:51
tags:
- nodejs
---
### 重構
在 tourController 裡有許多重複的 code，寫法並不清晰
目標：寫下將以下的 code 重構，變得易懂
![完整程式碼](https://github.com/Hazelwu2/nodejs-practice/commit/ba4f93c52ac32e7a46bfeb506827b14e20402dd2)

tourController.js
``` js
exports.createTour = async (req, res) => {
  try {
    const newTour = await Tour.create(req.body)

    res.status(201).json({
      status: 1,
      data: {
        tour: newTour,
      },
    })
  } catch (err) {
    res.status(400).json({
      status: 0,
      message: 'Invalid Data Sent!',
      err,
    })
  }
}
```
## 開始重構
### catchAsync


1. 建立一個 catchAsync 函數，並傳入參數 fn
為了避免每一段都是寫 try catch，顯得程式碼很亂，我們建立一個函數 catchAsync
來幫助我們簡單捕捉錯誤，並回傳一個匿名函數，將其分配給 createTour

catchAsync：捕捉 async function 的 Error，在 ES7 中 async function 會回傳 Promise，也就意味者：若 async function 捕捉到 Error Promise 會被 reject

2. 將 try, catch 程式碼移除
而原本的 createTour 將 try 裡的程式碼挪到 try 外，也就是下面範例程式碼註解的部分移除


``` js
const catchAsync = (fn) => (req, res, next) => {
  fn(req, res, next).catch(next)
}

exports.createTour = catchAsync(async (req, res) => {
  const newTour = await Tour.create(req.body)

  res.status(201).json({
    status: 1,
    data: {
      tour: newTour,
    },
  })
  // try {
  // } catch (err) {
  //   res.status(400).json({
  //     status: 0,
  //     message: 'Invalid Data Sent!',
  //     err,
  //   })
  // }
})
```

重構，將 catchAsync 獨立一個檔案
在 utils 建立 catchAsync.js，並 export module

``` js
module.exports = (fn) => (req, res, next) => {
  fn(req, res, next).catch(next)
}
```

在 tourController 引入，並將所有的路由加上 catchAsync
``` js 
const Tour = require('../models/tourModel')
const APIFeatures = require('../utils/apiFeatures')
const catchAsync = require('../utils/catchAsync')

exports.getTour = catchAsync(async (req, res, next) => {
  const tour = await Tour.findById(req.params.id)

  res.status(200).json({
    status: 1,
    message: 'success',
    data: {
      tour,
    },
  })
})

exports.createTour = catchAsync(async (req, res) => {
  const newTour = await Tour.create(req.body)

  res.status(201).json({
    status: 1,
    data: {
      tour: newTour,
    },
  })
})

exports.updateTour = catchAsync(async (req, res, next) => {
  const tour = await Tour.findByIdAndUpdate(req.params.id, req.body, {
    // [new] 設定 true 表示會返回修改後的 item ，而非原始 item
    new: true,
    // [runValidators] 若設定 true 將會觸發 Model Schema 重新驗證
    runValidators: true,
  })

  res.status(200).json({
    status: 1,
    message: 'success',
    data: {
      tour,
    },
  })
})

exports.deleteTour = catchAsync(async (req, res, next) => {
  await Tour.findByIdAndDelete(req.params.id)

  res.status(204).json({
    status: 1,
    message: 'success',
    data: null,
  })
})

```
### Reference
- [[Udemy] Nodejs Express Mongodb Bootcamp](https://www.udemy.com/course/nodejs-express-mongodb-bootcamp/learn/lecture/15065210)