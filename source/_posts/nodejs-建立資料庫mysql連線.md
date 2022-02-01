---
title: nodejs-建立資料庫mysql連線
date: 2022-01-31 12:57:23
tags:
category:
- nodejs
- sequelie
- mysql
---
建立資料庫連線

1. MySQL 建立 Todo 資料庫
2. Express 設定資料庫連線
  - 安裝 mysql2、sequelie、sequelie-cli
  - 初始化
    - 設定資料庫名稱、密碼
3. 設定 Model
  - Todo Model
  - User Model
4. 設定 Todo & User 關聯
5. 完成註冊功能

## MySQL 建立 Todo 資料庫
``` sql
drop database if exists todo;
create todo;
use todo;
```
輸入指令後，在MySQL WorkBench 按「⚡️」執行
![mysql](/../../../images/20220131/excute-mysql.png)

## Express 設定資料庫連線
安裝相關的套件
``` bash
npm install mysql2 sequelize sequelize-cli
npm run dev
```


初始化 sequelize 腳本
執行初始化指令會自動幫你建立檔案
```
npx sequelize init
```

順利設定成功，應該會出現下面的訊息
```
Sequelize CLI [Node: 12.6.0, CLI: 6.4.1, ORM: 6.15.0]

Created "config/config.json"
Successfully created models folder at "/Users/hazel/vhost/todo/models".
Successfully created migrations folder at "/Users/hazel/vhost/todo/migrations".
Successfully created seeders folder at "/Users/hazel/vhost/todo/seeders".
```
建立了哪些呢
- config/config.json，資料庫的設定檔
- todo/models，Model的設定檔
- todo/migrations，資料庫遷移檔，紀錄資料庫新增、修改哪些欄位，以和其他協作成員同步
- todo/seeders，資料庫種子檔，用來生成資料用

![npx-sequelize-init](../../../images/20220131/npx-sequelize-init.png)

自動生成的 config.json 檔
其中有三個區塊：development、test、production，各有不同的用途
![npx-sequelize-config-json](../../../images/20220131/squelize-config-json.png)

### 產生 Todo Model
設定Todo Modal
- name String 任務名稱
- isDone Boolean 是否完成

``` bash
npx sequelize-cli model:generate --name Todo --attributes name:string,isDone:boolean
```
輸入完後終端機會返回以下訊息
```
Sequelize CLI [Node: 12.6.0, CLI: 6.4.1, ORM: 6.15.0]

New model was created at /Users/hazel/vhost/todo/models/todo.js .
New migration was created at /Users/hazel/vhost/todo/migrations/20220131073523-create-todo.js .
```
自動建立了以下檔案
- models/todo.js Todo 的 Model 設定檔，可定義資料欄位、資料關聯
- todo/migrations/20220131073523-create-todo.js 資料庫遷移紀錄，可當作是Git版的資料庫，所有版本紀錄都會在 migrations 資料夾內

### 小知識：Migration 用處
當你在開發新功能，在 User 資料表多新增了一個欄位叫「memo」，
通知團隊成員說你新增了 memo 欄位，請大家同步
其他團隊成員在自己的本地環境也必須手動新增 User memo 的欄位
這樣團隊們才可以跑本地環境時不會因為自己新加上的功能，但自己本地資料庫沒有那個欄位，導致程式壞掉

只要有 migration 就不必這麼麻煩
Migration 是資料庫的版本紀錄，只需要跑一個指令，會自動將版本紀錄變更的欄位同步到你的資料庫

```
npx sequelize-cli db:migrate
```
成功後會自動產生 Todo 資料表及建立欄位。如果沒有成功，表示資料庫連線並未設立完全
```
$ npx sequelize db:migrate

Sequelize CLI [Node: 12.6.0, CLI: 6.4.1, ORM: 6.15.0]

Loaded configuration file "config/config.json".
Using environment "development".
== 20220131073523-create-todo: migrating =======
== 20220131073523-create-todo: migrated (0.019s)
```


### 命名規範
Todo、todos？傻傻分不清？
在 MVC 的設計裡，Todo model 的變數命名皆是用單數大寫「Todo」
而在 MySQL 的指令裡，則會用複數小寫 todos 來代表資料表的名稱


### Sqeuelize Migration
Migration 的概念就是資料庫的版本控制，當我們改資料庫的 Schema時都會去新增一個 migration 檔案
Sequeuelize會追蹤哪些 migration 已經合併，哪些還沒有使用過，並根據定義的 migration 去修改資料庫的欄位，又稱之為資料庫的版本控制。

一個 migration 一生中只會執行一次，migration 代表著每次對資料庫的修改紀錄，如果改錯了就再新增一次 migration 覆蓋過去即可。

用指令產生的檔案稱 migration，用動作來同步資料庫的 Schema 稱 migrate，一個名詞一個動詞，不要搞混。

資料遷移
通常會使用在初始化專案或團隊改動了資料庫 Schema，便會跑 migrate 來同步
```
npx sequelize db:migrate
```

查看現有版本
如果不清楚現在資料庫現在版本是多少，可以藉由這個指令更清楚了解現在在哪一個版本
```
npx sequelize db:migrate:status
```

還原到上一個版本
```
npx sequelize db:migrate:undo
```

還原到最初始版本
等同於作業系統重灌的概念
```
npx sequelize db:migrate:undo:all
```

還原到特定版本
等同於`git checkout [commitID]` 或是 `git reset --mixed [commitID]`
```
npx sequelize db:migrate:undo:all --to XXXXXXX-create-user.js
```



## 設定關聯 Model
- 一個 User 擁有多個 Todo
- 一個 Todo 屬於特定的 User

找到 assoicate的 function，這個區域是專門寫關聯的

model/user.js
``` js
static associate(models) {
  User.hasMany(models.Todo)
}

```
model/todo.js
``` js
static associate(models) {
  Todo.belongsTo(models.User)
}
```

建立關聯
命名清楚一點，加入「xxx」到「哪張表」
例如：我這次 migration 將 userId 欄位新增到 Todo 資料表，就會變成`add-userId-to-todo`，藉由黨名可以非常清楚這次遷移改變了什麼
```
npx sequelize migration:generate --name add-userId-to-todo
```

開啟剛產生的 add-userId-to-todo 檔案，加上關聯設定 
定義 reference，讓 Todos 欄位新增 UserId，且是來自 User 資料表裡的 ID
```
references: {
  model: 'Users',
  key: 'id'
}
```
完整程式碼
``` js
module.exports = {
  async up(queryInterface, Sequelize) {
    return queryInterface.addColumn('Todos', 'UserId', {
      type: Sequelize.INTEGER,
      allowNull: false,
      references: {
        model: 'Users',
        key: 'id'
      }
    })
  },

  async down(queryInterface, Sequelize) {
    return queryInterface.removeColumn('Todos', 'UserId')
  }
};
```

- addColumn：新增欄位，通常定義在 up 上
- removeColumn：移除欄位

## Reference
- [初始化 Sequelize](https://sequelize.org/master/manual/migrations.html#bootstrapping)