---
title: Gulp 學習紀錄、常用指令
date: 2017-11-04 06:23:55
tags:
---
# Gulp 學習紀錄、常用指令
紀錄學習Gulp的歷程，及gulp常用的指令

### 第一次安裝Gulp
1. npm install -g gulp
2. npm init
3. 在目錄下touch gulpfile.js
4. npm install 外掛名稱 —save-dev

### 之後使用Gulp
1. 移動到目錄後，npm init，創造package.json檔
2. 貼上已設定的package.json
3. npm install -l 自動安裝package.json列表上的外掛
4. 複製已設定好的腳本gulpfile.js

### 常用外掛清單

- SASS/SCSS編譯成CSS gulp-sass

- [CSS 壓縮迷你化 gulp-minify-css](https://www.npmjs.com/package/gulp-minify-css)

- [遇錯時不會中斷 gulp-plumber](https://www.npmjs.com/package/gulp-plumber)

- [後處理器 gulp-postcss](https://www.npmjs.com/package/gulp-postcss)

- [根據不同瀏覽器版本，自動補上前綴詞 autoprefixer](https://www.npmjs.com/package/autoprefixer)

- [不用寫一大堆的變數，用$取代 gulp-load-plugins](https://www.npmjs.com/package/gulp-load-plugins)

- [JavaScript ES6 編譯 gulp-babel](https://www.npmjs.com/package/gulp-babel)

- [壓縮合併程式碼後可以找出原始頭是誰 gulp-sourcemaps](https://www.npmjs.com/package/gulp-sourcemaps)

- [合併程式碼 gulp-concat](https://www.npmjs.com/package/gulp-concat)

- [外部載入的套件需要排序 gulp-order](https://www.npmjs.com/package/gulp-order)
(對jquery,bootstrap常載入的是必備品)

- [Web Server，即時預覽檔案，安裝後即不用裝liverRoad  ：Browser Sync](https://www.npmjs.com/package/browser-sync)

- [Gulp 串接 Bower 工具 main-bower-files](https://www.npmjs.com/package/main-bower-files)

- [JavaScript 壓縮工具 gulp-uglify](https://www.npmjs.com/package/gulp-uglify)

- [gulp 流程控制 minimist](https://www.npmjs.com/package/minimist)

- [設定任務，可加入判斷式 gulp-if](https://www.npmjs.com/package/gulp-if)


### 在學習Gulp時遇到的障礙

在安裝gulp時出現以下訊息

```npm WARN deprecated minimatch@2.0.10: Please update to minimatch 3.0.2 or higher to avoid a RegExp DoS issue```
- npm WARN已棄用minimatch@2.0.10：請更新至最低3.0.2或更高版本以避免RegExp DoS問題

- 解決辦法：npm install minimatch@“3.0.2”
或著不用理他也沒有關係的


------------

### Gulp 常用指令
`Npm init`
- 輸入npm init，進行這個空白專案的初始化，同時也會生成一個基本的package.json，這個package.json非常重要，因為它定義了這個 Node.js 專案裏頭會需要用到的模組與套件 ( Node modules )，輸入npm init之後，基本上可以填寫一些名稱或描述，不然其實也可以直接一直 enter 按下去就會建立完成。

`touch gulpfile.js`
- 建立gulpfile.js，要寫的任務、套件全部都是寫在這裡面

` npm install `
- 會安裝專案內的 package.json 所列出的需要元件。

`npm install -l `
- 把package.json腳本設定好，建立新專案時輸入即可依照package.json上列出的套件，全部自動化安裝完

### npm 相關指令整理
- http://dreamerslab.com/blog/tw/npm-basic-commands/
- [跟著教學操作的youtube](https://www.youtube.com/watch?v=1rw9MfIleEg)

### Reference | 參考網站
- http://www.oxxostudio.tw/articles/201503/gulp-install-webserver.html
- https://gulpjs.com/
- https://987.tw/gulpru-men-zhi-nan/
- http://www.gulpjs.com.cn/docs/getting-started/
- https://ithelp.ithome.com.tw/articles/10185565