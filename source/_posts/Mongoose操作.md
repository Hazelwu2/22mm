---
title: [Mongo] Mongoose操作
date: 2022-02-05 18:37:20
tags:
category:
- mongo
- mongoose
---
## findByIdAndUpdate

Mongoose 的 findByIdAndUpdate，會呼叫 MongoDB findAndModify 方法

- new: 設定 true 表示會返回修改後的 item ，而非原始 item
- runValidators: 若設定 true 將會觸發 Model Schema 重新驗證
- upsert：若設定 true，如果 object 不存在會新建，預設值是 false

``` js
// 引入 Model Instance
const Tour = require('../models/tourModel')

const tour = await Tour.findByIdAndUpdate(req.params.id, req.body, {
  // [new] 設定 true 表示會返回修改後的 item ，而非原始 item
  new: true,
  // [runValidators] 若設定 true 將會觸發 Model Schema 重新驗證
  runValidators: true,
})
```

``` js
await UserTrackNftList.findOneAndUpdate({ name: req.body.name },
  { $push: { last_data: result.data.collection.stats } },
  { upsert: true, new: true, setDefaultsOnInsert: true },
)
```
找到對應的資料後，僅更新 last_data 的資料欄位

## 瀏覽 Model 全部資料
- 取出 Post 所有資料，使用 Post.find()，因為沒有傳入參數，因此會撈出所有資料

``` js
const Post = require('./models/post') // 載入 Post model

app.get('/', (req, res) => {
  Post.find() // 取出 Post model 裡的所有資料
    .lean() // 將 Model 物件轉成 js 陣列
    .then(posts => res.render('index', { posts })) // 將資料傳給 index 樣板
    .catch(error => console.error(error)) // 錯誤處理
})
```

API 版本
``` js
const Post = require('./models/post') // 載入 Post model

app.get('/posts', async (req, res) => {

  try {
    const posts = await Post.find()

    res.status(200).json({
      status: 'success',
      data: {
        post
      },
    })
  } catch(error) {
    console.log(error)
  }
})
```

## 新增資料
使用 mongoose 提供的方法 Model.create 來建立資料

``` js
app.post('/posts', async (req, res) => {
  const name = req.body.name

  try {
    await Post.create({ name })     // 存入資料庫
    res.status(200).json({
      status: 'success',
      msg: '建立成功',
      data: { name }
    })
  } catch (error){
    res.status(400).json({
      statuss: 'fail',
      msg: error
    })
  }
})
```


## 瀏覽特定一筆資料
1. 資料庫查詢資料，使用 findById，使用此方法只會返回一筆資料，以 id 去尋找資料
2. 若撈完資料後，要回傳給樣板使用 res.render()，則要用 lean()，將資料整理乾淨。口訣：「撈資料以後想用 res.render()，就要先用 .lean()」。
- lean：把 Mongoose 的 Model 物件轉換成 Javascript 陣列


``` js
const Post = require('./models/post') // 載入 Post model

app.get('/posts/:id', (req, res) => {
  const id = req.params.id
  return Post.findById(id)
    .lean()
    .then((post) => res.render('item', { post }))
    .catch(error => console.log(error))
})
```

API 版本
``` js
const Post = require('./models/post') // 載入 Post model

app.get('/posts', async (req, res) => {
  const post = await Post.findById(req.params.id)

  if (!post) {
    return next(new AppError('No post found with that ID', 404))
  }

  res.status(200).json({
    status: 'success',
    data: {
      post
    },
  })
})
```

## 修改資料
1. 資料庫查詢資料，使用 findById，使用此方法只會返回一筆資料
2. 修改資料，post.name = name
3. 儲存資料，post.save()，針對修改的資料儲存

``` js
app.get('/posts/:id/edit', async (req, res) => {
  const id = req.params.id

  try {
      const id = req.params.id
      const name = req.body.name
      return Post.findById(id)
        .then(todo => {
          post.name = name
          return post.save()
        })
        .then(()=> {
          res.status(200).json({
            status: 'success',
            msg: '修改成功',
            data: post
          })
        })
        .catch(error => console.log(error))

  } catch (error) {
    res.status(400).json({
      statuss: 'fail',
      msg: error
    })
  }
})
```

## 刪除資料
1. 資料庫查詢資料，使用 findById，使用此方法只會返回一筆資料
2. 移除資料，post.remove()

``` js
app.post('/posts/:id/delete', (req, res) => {
  const id = req.params.id
  return Post.findById(id)
    .then(post => post.remove())
    .then(() => {
      res.status(200).json({
        status: 'success',
        msg: '刪除成功',
        data: post
      })
    })
    .catch(error => console.log(error))
})
```