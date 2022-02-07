---
title: Vue串接LineNotify
date: 2022-02-05 09:22:39
tags:
category:
- vue
---
## 前言
此篇介紹怎麼串接 Line Notify，使用 Vue 來示範，也會寫下我踩過的坑紀錄下來，因為那些坑搞了我快兩個小時...
串接 Line Notify 總共會打三隻 API，接下來的 API 路徑會省略 `https://notify-api.line.me`，直接說是 `oauth/authorize` 或 `/oauth/token`，以方便閱讀。

1. GET `https://notify-bot.line.me/oauth/authorize`
2. POST `https://notify-bot.line.me/oauth/token`
3. POST `https://notify-api.line.me/api/notify`


## 步驟一：請求 authorize
第一步驟 GET 請求 `/oauth/authorize`，請求參數 client_id 可從 Line Notify 後台取得
較為敏感的參數可以設定在 .env，透過 `process.env` 來取得
由於是在 vue 設定，環境變數開頭必須以 `VUE_APP` 來命名，否則會抓取不到

較為特別的是直接設定 `window.open` 來開啟連結，需另外設定 redirect_uri 的網址
當使用者成功登入後，Line 會將使用者跳轉回來的目標網址，順利請求成功會回傳 code、state

踩到的坑：redirect_uri 不可設定 line、notify 等相關單字，會導致導向回來變成 404 Not Found的畫面
我的路由都設定為 `localhost:9527/line` 或 `localhost:9527/notify`，由於這個關係（腦袋單字不多ORZ）我卡住了兩個小時......
最後導向回來的 redirect_uri 設定為 `localhost:9527/auth-line`，才成功，希望看了這篇文章的讀者可以避免這個坑

``` js
<template>
  <v-btn v-if="!isLogin" elevation="2" @click="loginEvent()">Line登入</v-btn>
</template>

export default {
  methods: {
    // 請求登入授權
    login() {
      // 必填
      let loginUrl = 'https://notify-bot.line.me/oauth/authorize?'
      loginUrl += 'response_type=code'
      loginUrl += `&client_id=${process.env.VUE_APP_LINE_CHANNEL_ID}`
      loginUrl += `&redirect_uri=${process.env.VUE_APP_LINE_REDIRECT_URL}` // 要接收回傳訊息的網址
      loginUrl += `&state=${this.stateCode}`
      loginUrl += '&scope=openid%20profile'
      // 選填
      loginUrl += '&nonce=helloWorld'
      loginUrl += '&prompt=consent'
      loginUrl += '&max_age=3600'
      loginUrl += '&ui_locales=zh-TW'
      loginUrl += '&bot_prompt=normal'

      window.open(loginUrl, '_self') // 轉跳到該網址
    }
  }
}
```


## 步驟二：請求 token
在上一個步驟時請求成功會順利拿到 `code`，需要將此參數也放入進去
第二步驟 POST 請求 `/oauth/token`，比較需要注意的是 content-type 需要設定為 `application/x-www-form-urlencoded`

我使用的是 axios，header `application/x-www-form-urlencoded` 格式需要另外處理
axios 數據都是 json 格式，要使用 `application/x-www-form-urlencoded` 格式要安裝 Qs 套件，用 `Qs.stringify()` 方法來轉換參數

1. 安裝 Qs
``` bash
npm i qs --save
```

2. 引用 Qs、使用方式
``` js
import Qs from 'qs'
// 示範
const params = Qs.stringify({
  name: 'hazel'
})

console.log(params)
```
以上是 Qs 使用範例

繼續回到專案範例
``` js
const params = Qs.stringify({
  grant_type: 'authorization_code',
  code: this.query.code,
  redirect_uri: process.env.VUE_APP_LINE_REDIRECT_URL,
  client_id: process.env.VUE_APP_LINE_CHANNEL_ID,
  client_secret: process.env.VUE_APP_LINE_CHANNEL_SECRET,
})

```


## Reference 
- [[axios] 處理 x-www-form-urlencoded 格式問題](https://jeremysu0131.github.io/axios-%E8%99%95%E7%90%86-x-www-form-urlencoded-%E6%A0%BC%E5%BC%8F%E5%95%8F%E9%A1%8C/)