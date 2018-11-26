---
title: Vue 元件化概念
date: 2018-11-26 22:30:47
tags:
---

## Vue 元件化概念 筆記
1. 使用元件化切出複雜頁面
2. 使用vue-router 做耊面
3. 使用 vuex（資料中心）統一狀態更動
![Alt text](./Screen Shot 2018-09-24 at 6.07.05 PM.png)

### 元件化
- App.vue是大元件
- 什麼是元件化？
- 將網頁依照頁面、功能拆分
- 小至一個按鈕、大到整個頁面或程式
- Single File Component
#### Vue 元件化概念
- 新增 / 使用元件
- 傳遞屬性 props
- 釋放事件 $emit
- 元件重用控制 :key

#### 新增 / 使用元件
每個元件都有自己的 data / computed / methods
data需要是回傳資料的函數（非物件）

```
Vue.component('post', {
	template:: {
		...
	},
	data(){
		...
	}
})
```

#### 傳遞屬性 props
[Vue Props Document](https://vuejs.org/v2/guide/components-props.html)
使用 v-bind:屬性 或 :屬性，傳值給元件
```
<component v-bind:value='10' :post=post><component>
```
元件收到值後，會當成內部資料使用
```
data: {
	props: ['propname'],
	data: {...}
}
或是
data: {
	props: {
		title: String
	}
}
```
propD: {
	dafault: 100
}

#### 釋放事件 $emit
``` javascript
<component @save='...'><component>

this.$emit('save', somedata)
```
釋出 save事件，並釋出一些 data
在元件的外面儲存，收到卡片@save的事件，事件save後，卡片資料放上去。

#### 重用控制 :key
```
<component @key='...'><component>
```
根據 key 有沒有改變，控制資料是否要變更。 

比如說控制卡片列表是否要重新渲染，是否要重新產生方塊
![Alt text](./Screen Shot 2018-09-24 at 6.20.37 PM.png)

雜學校的每個文章：卡片每個都有獨立的key，如果規則不同（標題不同），則重新產生卡片。
使用時機：文章建立、頁面建立時

## 初始化專案
- pug
- pug-loader
- pug-filters
- sass
### 安裝 Pug、Sass 套件
```
npm install pug pug-loader pug-filters --save
npm install sass sass-loader node-sass --save
```
- 指定開發語言
```
<template lang='pug'></template>
<style lang='sass'></style>
```
- 建立專案環境，專案名稱：vuecomp，採用webpack-simple樣板
```
vue init webpack-simple vuecomp
cd vuecomp
npm install //安裝相依性的套件
npm run dev // hot load方式開啟本機環境：localhost:8080
```
data不是 object，是函數喔
