---
title: '[Nodejs] Mongoose Virtual Properties'
date: 2021-06-28 00:44:06
tags:
---
# [Nodejs] Mongoose Virtual Properties
## 什麼是 Virtual Properties

它是 Mongoose 提供的功能之一，Model 可以在 Schema 定義虛擬屬性(Virtual Properties)，它並不會被存入資料庫，但是我們可以使用和讀取，這樣做為了可以節省空間

### 優缺點

- 優點：節省資料庫空間
- 缺點：由於Virtual 沒有儲存在資料庫中，因此無法查詢

它的概念其實就是 Vue 裡的 computed 屬性

例如：lastName, firstName，最常看到的範例是 Vue2 computed 設定 lastName, firstName, fullName

## 如何使用

定義了 tourSchema，在 tourSchema 最後定義完後加上設定 `toJSON: { virtuals: true }, toObject: { virtuals: true }`

```jsx
// 這是最後要加上的設定
toJSON: { virtuals: true }
// 舉例
const mongoose = require('mongoose')

const testSchema = new mongoose.Schema(
	{ ...自定義Schema }, 
	{
		toJSON: { virtuals: true },
	  toObject: { virtuals: true },
	}
)
```

### 設定虛擬屬性

```jsx
schema.virtual('定義虛擬屬性名稱').get(function() {
	// 這個值要 return 什麼
	return 'This is Virtual Properities'
})
```

在以下程式碼，設定了 durationWeeks，根據 duration / 7 而得來，後面要接的是 callback function

注意：不可用箭頭Function來寫，傳回的值會是 null

❌：get.(() ⇒ { this.duration / 7 })
🆗：get.(function() { return this.duration / 7 })

```jsx
tourSchema.virtual('durationWeeks').get(function () {
  return this.duration / 7
})
```

![Postman Final Result](/images/20210628-virtual-properties.png)

## 完整程式碼

```jsx
const mongoose = require('mongoose')

const tourSchema = new mongoose.Schema({
  name: {
    type: String,
    required: [true, 'A tour must have a name'],
    unique: true,
    trim: true,
  },
  duration: {
    type: Number,
    required: [true, 'A tour must hava a duration'],
  },
  maxGroupSize: {
    type: Number,
    required: [true, 'A tour must have a group size'],
  },
  difficulty: {
    type: String,
    required: [true, 'A tour must have a difficulty'],
  },
  price: {
    type: Number,
    required: [true, 'A tour must have a price'],
  },
  ratingsAverage: {
    type: Number,
    default: 4.5,
  },
  ratingsQuantity: {
    type: Number,
    default: 0,
  },
  priceDiscount: Number,
  summary: {
    type: String,
    trim: true,
    required: [true, 'A tour must have a description'],
  },
  description: {
    type: String,
    trim: true,
  },
  imageCover: {
    type: String,
    required: [true, 'A tour must have a cover image'],
  },
  images: [String],
  createdAt: {
    type: Date,
    default: Date.now(),
    select: false, // API 永遠不顯示在 Client 端
  },
  startDates: [Date],
}, {
  toJSON: { virtuals: true },
  toObject: { virtuals: true },
})

// eslint-disable-next-line func-names
tourSchema.virtual('durationWeeks').get(function () {
  return this.duration / 7
})

const Tour = mongoose.model('Tour', tourSchema)

module.exports = Tour
```