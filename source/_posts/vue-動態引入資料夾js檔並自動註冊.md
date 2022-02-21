---
title: vue-動態引入資料夾js檔並自動註冊
date: 2022-02-22 00:09:33
tags:
- context module
category:
- vue
- filters
---

在 filters 資料夾新增 js檔能達到註冊，不需要每次手動引入，會用到 Webpack Context Module API
希望盡可能讓 main.js 簡潔一些

原本在 main.js 註冊了三個 filter
``` js
// 幣值
import currencyFilter from '@/filters/currency'
Vue.filter('currency', currencyFilter)

// 時間
import timeFilter from '@/filters/time'
Vue.filter('time', timeFilter)

// 時間
import numberFilter from '@/filters/filter'
Vue.filter('time', numberFilter)
```

這讓我們可以在 Vue 模板裡使用
``` html
<div>{{ amount | current  }}</div>
```

但會有個缺點，每次新增 filters 時需要再到 main.js 檔都要重新引入，導致 main.js 又大又肥
以下做法可以自動引入，讓 main.js 簡潔有力

### context module API
Webpack 官方文件說明
```
A context module exports a (require) function that takes one argument: the request.

The exported function has 3 properties: resolve, keys, id.

resolve is a function and returns the module id of the parsed request.
keys is a function that returns an array of all possible requests that the context module can handle.
```

context module  可以幫助我們從某個資料夾找出所有的文件，也能用正則去匹配
下面範例就是匹配出所有 components 資料夾下的 js 檔

``` js
require.context('./components', false, /\.js$/);
```

## 建立 filters.js
在 src/utils目錄下新增 filters.js 檔，利用 context 引入 filters 資料夾下所有的 js 檔

``` js
const modulesFiles = require.context('../filters', true, /\.js$/)
```

## 整理 filters 各隻 js 輸出格式
編輯各隻 filters 資料夾下的 js 檔，統一使用 `export default {} ` 匯出一個物件

例如 filters/number.js 檔案內容
``` js
function thousandsFixedTwo () { ... }
function numberWithCommas () { ... }

export default {
  thousandsFixedTwo,
  numberWithCommas
}

```
其他凡是在 filters 下的 js，都要統一變成以上格式去 export

## 宣告 modules
整理完格式後，回到 src/utils/filters.js，宣告 modules 變數，找所有 filters 資料夾下的 js 檔匯集成所有集結的物件
``` js
const modules = modulesFiles.keys().reduce((modules, modulePath) => {
  // set './app.js' => 'app'
  const moduleName = modulePath.replace(/^\.\/(.*)\.\w+$/, '$1')
  const value = modulesFiles(modulePath)
  modules[moduleName] = value.default
  return modules
}, {})
```
所以 filters.js 檔案內容會變成這樣

``` js
// https://webpack.js.org/guides/dependency-management/#requirecontext
const modulesFiles = require.context('../filters', true, /\.js$/)

const modules = modulesFiles.keys().reduce((modules, modulePath) => {
  // set './app.js' => 'app'
  const moduleName = modulePath.replace(/^\.\/(.*)\.\w+$/, '$1')
  const value = modulesFiles(modulePath)
  modules[moduleName] = value.default
  return modules
}, {})
```

可以下 `console.log(modules)` 查看他的值，可以發現 modules 會變成像以下的資料型態

``` js
{
  bankaccount: {
    accountTransferToAsterisk() {}
  },
  currency: {
    transferMoney() {}
  },
  number: {
    thousandsFixedTwo() {...},
    numberWithCommas() {...}
  },
}
```

接著 filters.js 再用迴圈方式每個自動註冊即可達成我們的需求，自動註冊 filters

``` js
export default {
  install(Vue) {
    Object.entries(modules).forEach((item, i) => {
      Object.keys(item[1]).forEach(h => {
        Vue.filter(item[0], item[1][h])
      })
    })
  }
}
```

完整的 filters.js 檔案
``` js
// https://webpack.js.org/guides/dependency-management/#requirecontext
const modulesFiles = require.context('../filters', true, /\.js$/)

const modules = modulesFiles.keys().reduce((modules, modulePath) => {
  // set './app.js' => 'app'
  const moduleName = modulePath.replace(/^\.\/(.*)\.\w+$/, '$1')
  const value = modulesFiles(modulePath)
  modules[moduleName] = value.default
  return modules
}, {})

export default {
  install(Vue) {
    Object.entries(modules).forEach((item, i) => {
      Object.keys(item[1]).forEach(h => {
        Vue.filter(item[0], item[1][h])
      })
    })
  }
}
```

## 引入 filters.js
但別忘了要回到 main.js 引入 filters.js
``` js
import Vue from 'vue'
// 註冊 filters
import Filter from '@/utils/filters'
Vue.use(Filter)
```

## Reference
- [Webpack Document - require.context](https://webpack.js.org/guides/dependency-management/#requirecontext)