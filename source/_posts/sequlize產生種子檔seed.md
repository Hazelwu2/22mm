---
title: sequlize 筆記
date: 2022-01-31 18:15:38
tags:
category:
- sequlize
---
## 初始化
使用 Sequelize CLI 時，會用 npx sequelize init 完成初始化
```
$ npm install --save-dev sequelize-cli
$ npx sequelize init
```
為什麼會使用 npx 來呼叫，而非 npm？
如果我們在專案內呼叫 sequelize，會從專案內的 `node_modules 裡的 .bin` 資料夾找 sequelie 相關執行檔，也就是 `node_modules/.bin/sequelize` 

安裝 npm 5.2版開始，也會附帶安裝 npx 工具包，npx的功用是「自動定位套件真正的安裝路徑」。
每個人電腦環境設定不同，有些人設定套件執行檔並非安裝在 `node_modules/.bin`上，在執行 sequelize 前必須先搞懂套件安裝的路徑在哪裡，才能去呼叫，因此才有了 `npx` 的工具存在。
以下介紹的指令皆會由 npx 來呼叫

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
- [阮一峰-npx 使用教程](http://www.ruanyifeng.com/blog/2019/02/npx.html)