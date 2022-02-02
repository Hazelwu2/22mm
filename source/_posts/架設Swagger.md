---
title: 架設Swagger在 nodejs express
date: 2022-02-02 15:12:21
tags:
category:
- swagger
- express
---
開發環境採用 
- Nodejs 12.6.0
- Express 框架
- MacOS

我並未安裝 swagger-jsdoc，swagger-jsdoc是寫在註解內會自動產生 API 文件，但註解會越來越長，沒有很喜歡，只會另外用 swagger.yml 維護而已，相對簡單。

## 安裝
``` bash
npm i swagger-ui-express --save
```

## app.js
在 app.js 引入套件
有分成 json、yml 的檔來撰寫 API 文件，以下會提供兩種範例，引入 yml 需要另外安裝 yamljs 套件

### 引入 json 方式
``` js
const express = require('express')
// Swagger
const swaggerUi = require('swagger-ui-express');
const swaggerDocument = require('./swagger.json');
// Application
const app = express()
```

### 引入 yaml 方式
在 src 根目錄建立 swagger.yml 檔，並用 `npm install yamljs`，才可引入 yml 檔
```
npm i yamljs --save
```
引入 yaml
``` js
const express = require('express')
// Swagger
const YAML = require('yamljs')
const swaggerDocument = YAML.load('./swagger.yml')
// Application
const app = express()
```

引入相關套件後，接著在 app.js 使用 `app.use` 方法，`/apidoc` 可改成你想要的路徑

``` js
// API Document
app.use(
  '/apidoc',
  swaggerUi.serve,
  swaggerUi.setup(swaggerDocument)
);
```

接著重啟 server，如果 server 是在 localhost:3000，則 swagger api 網址是 `localhost:3000/apidoc`。個人較喜歡 yml 簡潔的模式，故引用的是 swagger.yml

完整程式碼
``` js
const express = require('express')
// Swagger
const YAML = require('yamljs')
const swaggerDocument = YAML.load('./swagger.yml')
// Application
const app = express()

// API Document
app.use(
  '/apidoc',
  swaggerUi.serve,
  swaggerUi.setup(swaggerDocument)
);
```

### 範例檔
以下提供簡易的 swagger.json、swagger.yaml 範例檔

``` json
{
  "swagger": "2.0",
  "info": {
    "version": "1.0.0",
    "title": "API 文件",
    "description": "API文件",
    "license": {
      "name": "MIT",
      "url": "https://opensource.org/licenses/MIT"
    }
  },
  "host": "localhost:10991",
  "basePath": "/",
  "tags": [{
    "name": "Users",
    "description": "API for users in the system"
  }],
  "schemes": ["http"],
  "consumes": ["application/json"],
  "produces": ["application/json"]
}
```

yml 範例檔
``` yml
openapi: 3.0.0
info:
  title: API 文件
  description: Optional multiline or single-line description in [CommonMark](http://commonmark.org/help/) or HTML.
  version: 0.1.0
servers:
  - url: http://api.example.com/v1
    description: Optional server description, e.g. Main (production) server
  - url: http://staging-api.example.com
    description: Optional server description, e.g. Internal staging server for testing
paths:
  /users:
    get:
      summary: Returns a list of users.
      description: Optional extended description in CommonMark or HTML.
      responses:
        '200':    # status code
          description: A JSON array of user names
          content:
            application/json:
              schema: 
                type: array
                items: 
                  type: string
```
### 客製化 CSS
Swagger UI 也有提供客製化功能
``` js
const cssOptions = {
  customCss: `
  .topbar-wrapper img {content:url(https://img.icons8.com/doodle/2x/-freelancefirm.png); width:50px; height:auto;}
  .swagger-ui table { margin-left: 20px;
    margin-top: 30px; }
  .swagger-ui .model { font-size: 14px;
    font-family: -apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,"Helvetica Neue",Arial,"Noto Sans","Liberation Sans",sans-serif,"Apple Color Emoji","Segoe UI Emoji","Segoe UI Symbol","Noto Color Emoji"; }
  .swagger-ui .topbar { background-color: #000000; border-bottom: 20px solid #5dc6d1; }`,
  customSiteTitle: "API文件 | Hazel"
};

app.use('/docs', swaggerUi.serve, swaggerUi.setup(swaggerSpec, cssOptions));
```

### 踩坑紀錄
Hazel 專案設定 app.js 之外，另外建立了 server.js，主要是用來連接 mongoose 資料庫及啟動伺服器 `app.listen`，我用上述方法寫在 server.js 沒有成功，到 localhost/apidoc會顯示找不到路由，但將 code 引入到 app.js 就成功了...原因不得而知

### Reference
- [How to add Swagger UI to an existing Node.js and Express.js project](https://levelup.gitconnected.com/how-to-add-swagger-ui-to-existing-node-js-and-express-js-project-2c8bad9364ce)
- [註解寫在Code內生成swagger UI](https://easonwang.gitbook.io/web_advance/api-ce-shi/swagger/zhu-jie-xie-zai-code-nei-sheng-cheng-swagger-uii)
- [使用 Swagger 自動生成 API 文件](https://israynotarray.com/nodejs/20201229/1974873838/)