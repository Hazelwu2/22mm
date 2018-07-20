---
title: Rails 與 Webpacker 神奇的結合：用image-wepback-loader 來壓縮圖片
date: 2018-04-28 03:51:02
tags:
- Rails 5
- Rails with Webpacker
- image compress
- image-webpack-loader
---

## Gem Webpacker 特色
> Webpacker是一個Gem，即使不用裝webpack，也能在Rails上編譯，然後也拿掉原生使用的Srocket。
Webpacker使用的是webpack 3.x.x版本，他已經預設幫你安裝了幾件事，但你需要另外設定檔

- 用babel 將ES6 編譯成 ES5
- 在webpack.yml上即可輕鬆設定好entry（入口）、output（出口）
- PostCSS，加入前綴詞
- CDN 支援
- 前端框架（React、Agnular、Elm、Vue）支援
- 內建 Rails view的helper

> 像用babel轉成ES5、PostCSS加前綴詞，只需要在專案上加入`.babelrc`、`postcss.config.js`做設定即可，當然package.json也需要安裝相關的套件才能跑

這邊不暫述怎麼在 Rails 5 初始化webapcker的設定，只介紹怎麼載入loader

1. 先讓webpacker能讀取設定路徑內，所有的圖片
2. 再讓webpacker載入loader

## 安裝及設定
### webpacker.yml設定entry、output
我個人設定是把所有image、js、css檔都放在app/frontend下
```
default: &default
  source_path: app/frontend
  source_entry_path: packs
  public_output_path: packs
  cache_path: tmp/cache/webpacker
```


### frontend/application.js 先載入所有圖片設定


```
# app/frontend/application.js
require.context('../images/', true, /\.(gif|jpg|png|svg)$/i);
require.context('../styles/', true, /^\.\/[^_].*\.(css|scss|sass)$/i);
```
Webpacker這個Gem，他已經預先使用`CSS loader`、`Sass loader`了
這項設定會使你專案內（app/frontend）的圖片、CSS等，都會被載入

在安裝webpacker時，他也會預先幫你設好目錄，在config/webpack檔案分別有`development.js`、`environment.js`、`production.js`。

### 安裝image-webpack-loader
圖片壓縮須先安裝image-webpack-loader，我們將透過npm來安裝這個loader。
輸入 `npm install image-webpack-loader`

### 載入image-webpack-loader
確認安裝完後，我們便來開始設定：載入image-webpack-loader
在`config/webpack/environment.js`下設定，options可以客製化，可以找image-webpack-loader的官方文件看更詳細的設置教學。

```
# config/webpack/environment.js
environment.loaders.append('image', {
  test: /\.(gif|png|jpe?g|svg)$/i,
  loader: 'image-webpack-loader',
  options: {
    mozjpeg: {
      progressive: true,
      quality: 65
    },
    // optipng.enabled: false will disable optipng
    optipng: {
      enabled: false
    },
    pngquant: {
      quality: '65-90',
      speed: 4
    },
    gifsicle: {
      interlaced: false
    },
    // the webp option will enable WEBP
    webp: {
      quality: 75
    }
  }
});
```
### 重新編譯 Webpacker
以上皆完成後，在cmd輸入以下指令，做重新編譯的動作
`webpack -w`或`ruby ./bin/webpack-dev-server`
你就會發現，圖片被壓縮了！！（撒花）



## Reference
- [Webpacker官方文件](https://github.com/rails/webpacker)
- [基於 Webpack 引入公共庫的幾種方式](https://hk.saowen.com/a/1650bf12d731afabf95a8bfa2cd885ddd944aa5e4e067aa75eb0ee5c2c75fcfe)

