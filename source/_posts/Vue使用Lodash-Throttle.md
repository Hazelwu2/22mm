---
title: Vue使用 Lodash Throttle，動態設定秒數
date: 2022-02-07 10:37:40
tags:
category:
- vue
- lodash
---

## 需求
接到一個需求，請求 API 取得延遲秒數，也就是說延遲秒數是動態設置的。
再開始設定像是註冊、登入等按鈕點擊後需要請求 API，用 Lodash 的 throttle 函式來延遲請求

## 常見寫法
在實作這個需求時有卡住一下，大部分的寫法都是像這樣
``` js
<template>
<button @click="onSubmit">送出</button>
</template>

export default {
  methods: {
    onSubmit: _.throttle(function () {
      // ...執行某些事
    }, 2000),
  }
}
```
秒數皆是設定靜態，並不符合我的需求。
但將 2000 取代成 this.click_interval，會發生錯誤，無法找到 this，如下範例
``` js
<template>
<button @click="onSubmit">送出</button>
</template>

export default {
  methods: {
    onSubmit: _.throttle(function () {
      // ...執行某些事
    }, this.settings.click_interval, // 無法執行，會找不到 this
  }
}
```

## 解決方案
最後嘗試了很久，要改為在生命週期宣告一個變數並用 function 儲存進去，這樣的寫法才能帶入動態秒數
從 API 取得的秒數，我儲存在 vuex getters 裡，`getters.settings.click_interval`

- methods 宣告 debouncer function，並什麼都不寫
- 宣告原本欲要請求 API 的方法 `this.clickToTransferIn()`，動態帶入 API取得的 click_interval，並在 function 裡執行 clickToTransferIn
- 在生命週期 created 執行 this.debouncer，並用 _.throttle


``` js
import { throttle } from 'lodash'
import { mapGetters } from 'vuex'

export default {
  created() {
    // 延遲按鈕：一鍵轉入
    this.debouncer = _.throttle(function(code, name) {
      this.clickToTransferIn(code, name)
    }, this.settings.click_interval * 1000 || 2000)
  },

  computed: {
    ...mapGetters(['settings']),
  },

  methods: {
    // 不可刪除，用於的按鈕延遲功能
    debouncer() {},

    // 請求 API
    async clickToTransferIn(code, name) {
      // ... 請求 API
      await requestTransferInAPI(params)
    }
  }
}
```
