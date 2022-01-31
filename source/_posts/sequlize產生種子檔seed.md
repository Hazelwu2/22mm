---
title: sequlize 筆記
date: 2022-01-31 18:15:38
tags:
category:
- sequlize
---

## Seequelize 指令

``` bash
# 安裝 sequelize-cli
$ npm install --save-dev sequelize-cli

# 建立 User Model，屬性 firstName, lastName, email
$ npx sequelize-cli model:generate --name User --attributes firstName:string, lastName:string, email:string

# 跑 Migration
$ npx sequelize-cli db:migrate
# Migration 倒退
$ npx sequelize-cli db:migrate:undo
# Migration 退回指定版本
$ npx sequelize-cli db:migrate:undo:all --to XXXXXXXXXXXXXX-create-posts.js

# 產生 Seed
$ npx sequelize-cli seed:generate --name default-data
# 執行所有 Seed 檔
$ npx sequelize db:seed:all
# 加上 Undo 執行 Down 動作，復原
$ npx sequelize db:seed:undo:all
# Undo 復原取消產生某一個 Seed
$ npx sequelize-cli db:seed:undo --seed name-of-seed-as-in-data
```

## Model Querying 查詢
``` js
// 建立一筆資料
const hazel = await User.create({ firstName: 'Hazel', lastName: 'Wu' })

// 查詢多筆資料，raw: true, nest: true，將多筆資料轉換物件
Todo.findAll({
  raw: true,
  nest: true
})

// 查詢特定屬性，等同於 SELECT firstName from ...
Model.findAll({ attributes: ['firstName'] })
// 查詢指令並加上 WHERE，等同於 SELECT * FROM user WHERE userId = 2;
Model.findAll({ where: userId: 2 })

// 用條件查詢一筆資料
Model.findOne({ where: { email } })
// 用 id 查詢一筆資料
Model.findByPk()
```

## Reference
- [官方文件 Model Querying - Finders](https://sequelize.org/master/manual/model-querying-finders.html)