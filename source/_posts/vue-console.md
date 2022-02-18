---
title: vue-console
date: 2022-02-18 15:58:24
tags:
category:
- Vue
---
獨立 console 檔案，做成 Plugin 來引用。在 Vue 專案內只要呼叫 this.$log, this.$error, this.$warn 即可 debug，也不用擔心忘記移除 log 上到生產環境

.env.stage, .env.master, .env.dev 個別設定變數 VUE_APP_OPEN_CONSOLE，console 印出根據 VUE_APP_OPEN_CONSOLE 環境變數設置  true 或 false，要注意的是 process.env.VUE_APP_OPEN_CONSOLE 取得的是字串 'true' 而非 Boolean

console.js
``` js
const Console = {
  $log: (...params) => console.log(...params),
  $error: (...params) => console.error(...params),
  $warn: (...params) => console.warn(...params)
}

export default {
  install(Vue) {
    Object.keys(Console).forEach(key => {
      Vue.prototype[key] = (...params) => {
        if (process.env.VUE_APP_OPEN_CONSOLE !== 'true') {
          return Console[key](...params)
        }
      }
    })
  }
}
```

接著回到 main.js
``` js
import Vue from 'vue'
import Console from '@/utils/console'
Vue.use(Console)
```

未來在元件上 debug，可以像下面這樣寫
``` js
export default {
  created() {
    this.$log('console.log')
    this.$warn('console.warn')
    this.$error('consoe.error)
  }
}
```