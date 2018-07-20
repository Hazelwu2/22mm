---
title: 你一定有聽過的Bower 前端套件管理工具
date: 2017-11-06 06:21:35
tags:
---
  
# 你一定有聽過的 Bower 前端套件管理工具
用Bower自動下載jQuery, Bootstrap等Libery，再與Gulp做串接
>bower init後安裝的套件，會自動寫出到bower.json檔案。
>全自動化，讓簡單且重複的事交給Gulp & Bower，把寶貴的時間拿來寫CODE吧！

此文章會用到下列幾個套件
 - `gulp-load-plugins` 以$字號來簡略載入各套件，只對gulp開頭的套件有用
 - `gulp-order` 排載入的順序
 - `main-bower-files` 和Bower結合
 - `gulp-concat` 合併檔案

  
```
    npm install -g bower
    cd project #進入專案資料夾
    bower init #bower初始化
    bower install <package>
    bower install jquery
    bower install bootstrap
```
`bower init` 後會產生 bower.json檔

`bower install <package>` 會自動產生bower_components資料夾，裡面放的       是下載下來的外掛。 

## 串接前端的Gulp自動化工具，整合
#### 需要用到main-bower-files的Gulp套件
第一步，在Gulpfile.js require後，輸入npm install寫入package.json內
```
var mainBowerFiles = require('main-bower-files');
npm install --save-dev main-bower-files
```
Bower幫前端工程師下載套件 => 不用再花時間一個個手動抓
整合到Gulp，將套件輸出到我們想要的資料夾 => 打gulp後自動跑全自動化！
**這就是為什麼需要整合，把簡單且重複的事交給電腦做吧！**

在package.json下多新增bower任務

```
gulp.task('bower', function() {
    return gulp.src(mainBowerFiles())
        .pipe(gulp.dest('./.tmp/vendors'))
});
```
再多新增一個任務叫**vendorJs**，最主要目的是輸出到public/vendors資料下之外，還需要賦予兩個任務：按照順序載入js、合併成一隻js檔。
- **照順序載入js：**像jquery, bootstrap是需要按照順序載入js的，所以需要另外安裝套件order。
- **合併成一隻js檔**：減少伺服器的request，讓伺服器負荷減輕，網站也能跑較快。

    gulp.task('vendorJs', [bower], function(){
	    return gulp.src([./.tmp/vendors/**/*.js])
				  .pipe($.order([
					'jquery.js',
					'bootstrap.js'
				   ]))
				  .pipe($.concat(vendors.js))
				  .pipe(gulp.dest('./public/js'))
      
    })
  
 
`gulp.task('vendorJs', ['bower'], function({..}))`

 多加入了`[bower]`的參數，代表在`vendorJs`任務跑完前，必須要等bower跑完。因為bower任務就是為了把下載回來的套件輸出的.tmp/vendors資料夾，若還沒下載回來，vendorJs就讀取不到啦！所以一定要記得加上。

## 將vendorJs加入default監控任務
default就不用額外設定bower任務了，因為vendorJs開跑前會先等bower寫完。

    gulp.task('default', ['vendorJs', 'browserSync', 'watch'])

輕鬆寫完腳本後，打上gulp便會自動產生囉！
可以將固定專案常用的腳本上傳到git，或是給其他開發者使用。


    #.gitinore 忽略
    bower_components
    node_modules
    public
開發者下載回來，只要輸入`npm install`及`bower install`即會產生一樣的效果，快速又方便。


----------
### bower_components資料夾，想要重新命名
若覺得`bower install`放套件資料夾「.bower_components」名字很醜，想另外取名的話其實是可以的哦！在專案資料夾根目錄下創建.bowerrc檔 `touch .bowerrc`
創建後，在bowerrc裡加入以下程式碼
``` 
	 .bowerrc
	 {
	  "directory": "vendors/"
	  }
```
接著執行 `bower install`便會產生vendors資料夾囉。

### 參考網站
- [Bower官方](https://bower.io/)
