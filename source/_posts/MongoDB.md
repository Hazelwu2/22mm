---
title: MongoDB
date: 2022-04-08 20:32:32
tags:
category:
- Mongodb
---

Mongodb 屬於 NoSQL，哪些適合用 NoSQL 存取
各種不
- 系統儀表板、聊天室、即時股票資訊、遊戲的個人狀態列

Mongo 侷限
- Document 不可超過 16M


## 安裝 Mongo、GUI
- [Mongo 安裝](https://www.mongodb.com/try/download/community)
- [GUI Compass 安裝](https://www.mongodb.com/try/download/compass)

## 開啟 mongo 資料庫
- 新增存放 Mongo DB 的 data 資料夾、log 檔案
  - mkdir data
  - touch data/mongodb.log 
- 執行指令
```
$ mongod --dbpath [data資料夾] --logpath [log資料夾]
$ mongod --dbpath /Users/hazel/vhost/side-project/mongodb/data --logpath /Users/hazel/vhost/side-project/mongodb/data/mongo.log
```

## 常用指令
資料庫顯示
``` bash
mongo # 進入 Mongo 指令
show dbs # 顯示資料庫 
# 切換資料庫
use [資料庫名稱]
use hotels # 切換資料庫 hotels
show collections # 顯示 DB 內所有的 Collections

# 新增 Collections rooms
db.createCollection(collection名稱);
db.createCollection('rooms');

# 新增單筆資料 insertOne
db.rooms.insertOne(
  { "name": "單人房", "price": 1200, "rating": 4.5 }
)
# 新增多筆資料 insertMany
db.rooms.insertMany(
  [
    {
      "rating": 4.2,
      "price": 4500,
      "name": "豪華單人房"
    },
    {
      "rating": 4.5,
      "price": 3000,
      "name": "雙人房"
    }
  ]
)
# updateOne 修改部分欄位，第一個參數 filter，第二個參數：變更值
db.rooms.updateOne(
  { _id: ObjectId(6250303fc1795946f9371a7f)},
  { $set: { name: "修改成超級豪滑雙人房" } }
)
# updateMany
db.rooms.updateMany(
  { type: "group" }
  { $set: { content: "測試" }}
)
# replaceOne 覆蓋全部欄位(替換整個Document)，updateOne: 允許更新欄位
# 替換整個文檔：replaceOne、replaceMany
db.rooms.replaceOne(
  { "_id":ObjectId("621edf99a20aa7506a116f9a") },
  { "name": "Hazel" }
)

# 刪除 Collection 所有資料
db.rooms.deleteMany({})
# 刪除多個文件 deleteMany (filter)
db.rooms.deleteMany(
  { rating: { $gte: 4.3 } 
})
```

## find 用法
- $eq: 等於
- $ne: 不等於
- $gt: 大於
- $lt: 小於
- $gte: 大於等於
- $lte: 小於等於
- $in: 存在某個值
- $nin: 不存在某個值

### 邏輯運算子 or 、 and
- $or: find({ $or: [{}, {}] })
- $and: find({ $and: [ {}, {}]})

``` js
db.rooms.find(
  {
    $and: [
      { price: { $lte: 1500 }},
      { rating: { $gte: 4.5 }}
    ]
  }
)
```


``` bash
# 模糊搜尋  name 裡面含有 c 字元的 document
db.collections.find({ name: /c/ })
# Project 保護欄位，隱藏 _id
db.posts.find({"name": "Hazel" },{"_id":0})
# 搜尋陣列裡面的值
db.posts.find({"payment":{ $in:["信用卡"] }})

# 查詢 陣列裡的值，有 謎因 或(or) 幹話 的 document 列表
db.posts.find({
  $or: [
    { tags: { $in: ["謎因"]}},
    { tags: { $in: ["幹話"]}}
  ]
})

# 查詢 comments 有超過 500(含)以上的 document 列表
db.posts.find({ comments: { $gte: 500 }})
# 查詢`name` 欄位為 `"Hazel"` ，filter 篩選出介於 500~1000(含) 個讚
db.posts.find(
  { $and: [
    { name: "Hazel" },
    { likes: { $gte: 500 }},
    { likes: { $lte: 1000 }}
  ]}
)
# 查詢 name: Hazel，排序由新到舊
db.posts
  .find({ name: "Hazel" })
  .sort({ createdAt: -1})

# name: Hazel，只顯示前 30 筆資料
db.posts.find({ name: "Hazel" }).limit(30)
# 移除 tags 陣列裡的情緒資料 $pull
db.posts.updateMany(
  { tags: { $in: ["感情"] }},
  { $pull: { "tags": "感情" }}
)
```
