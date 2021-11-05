---
title: 解決 Cannot read property range of null 錯誤
date: 2021-10-21 23:29:28
tags:
- vue
---

# 解決 Cannot read property 'range' of null 錯誤

原因：.eslintrc.js 設置的 parser: 'babel-eslint'
``` js
parserOptions: {
  parser: 'babel-eslint',
  ecmaVersion: 2017,
  sourceType: 'module'
},
```

之所以報錯，原因來自於 Router import 元件採用動態引入的方式
``` js
component: () => import(`@/views/${param}/5.index/home`) // 首頁

{
  path: '/preferentialActivity',
  name: 'preferentialActivity',
  component: () =>
    import(
      /* webpackChunkName: "preferential-activity" */ `../views/${param}/2.preferentialActivity`
    ), // 優惠活動
  meta: {
    requiresAuth: true
  }
}
```
網路上查到兩種解法，第一種是降低babel-eslint 的版本、第二種是改懶加載的寫法

先說第二種，改寫懶加載寫法

將 `() => import` 懶加載改成舊寫法便可以成功，但問題在於 `resolve => require` 這種寫法無法使用 Webpack 異步分組模塊 webpackChunkName，神奇的註解可以打包時，將相同元件打包成同一隻 js。由於專案日漸龐大，需要懶加載但又不希望分太多隻 js，所以這個方法雖然可以解決問題，但並不採用

``` js
// 改寫成 resolve => require
component: resolve => require([`@/views/${param}/5.index/home`], resolve)
```

看官方文件 babel-eslint，發現已不支援
```
babel-eslint is now @babel/eslint-parser. This package will no longer receive updates.
@babel/eslint-parser
```

## 無效方法
### babel-eslint 升級版本 11.0.0-beta.2 -D
```
npm i babel-eslint@11.0.0-beta.2 -D
```
宣告無效

#### babel-eslint 升級版本 10.0.3
```
npm i babel-eslint@10.0.3 -D
```
宣告無效

### 安裝 @babel/plugin-syntax-dynamic-import
``` bash
npm install --save-dev @babel/plugin-syntax-dynamic-import
```
宣告無效

### 安裝 @babel/plugin-syntax-dynamic-import
```
plugins: ["@babel/plugin-syntax-dynamic-import"]
```
宣告無效


###

```
npm install --save-dev @babel/eslint-parser @babel/eslint-plugin
```



## Reference
- [解決Cannot read property 'range' of null 錯誤](https://www.twblogs.net/a/5ee5c8ceb4c39c99158fb321)
- [vue路由懒加载报错](https://segmentfault.com/q/1010000020957304)