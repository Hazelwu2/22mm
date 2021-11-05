---
title: '[Vue] 錯誤 @import directive requires a url or quoted'
date: 2021-10-15 17:56:11
tags:
- vue
---
# 錯誤 @import directive requires a url or quoted path
## 發生情況
在 config.js 引入 scss 檔案 (color.scss) 作為全域使用時，出現 @import directive requires a url or quoted path 錯誤

- - - -
## 發生原因
 scss 檔案 (color.scss) 裡引入了外部網址
```scss
@import url("https://fonts.googleapis.com/css?family=Roboto:400,500,700")
```

sass-resources-loader 會替換成內部路徑，造成錯誤
```scss
../assets/css/url/https://fonts.googleapis.com/css?family=Roboto:400,500,700
```

- - - -
## 解決方法
### 方法一
	- 不要在此檔案 (color.scss) 引入外部連結，改在別的檔案引入，並在 main.js 引入
### 方法二
	- 在 index.html CDN 引入

- - - -
## 無效方法
文章提到的改引入方法，試驗後無效
``` 
// 原本
@import "https://fonts.googleapis.com/icon?family=Material+Icons"
// 更改後無效
@import url(https://fonts.googleapis.com/icon?family=Material+Icons)
```

- - - -
## 參考資料
- https://github.com/webpack/webpack/issues/8865

#vue/config/sccss