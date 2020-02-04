---
title: Vue 自動化導入 SASS Mixin
date: 2020-01-15 16:32:58
tags:
---

專案需要導入全域的 Mixin，以下會介紹要怎麼設定。專案使用的是Vue CLI 3建立的，因此需要自行建立一個`vue.config.js`檔來修改Webpack設定

在[官方Vue文件的自动化导入](https://cli.vuejs.org/zh/guide/css.html#%E9%A2%84%E5%A4%84%E7%90%86%E5%99%A8)說明，我們可以用style-resourses-loader來加載mixin、color等需要全域使用的CSS檔。


安裝 style-resources-loader
```
npm install style-resources-loader
```
接著在專案目錄最外層新增`vue.config.js`
``` js
// vue.config.js
const path = require('path');

module.exports = {
  chainWebpack: config => {
    const oneOfsMap = config.module.rule('scss').oneOfs.store
    oneOfsMap.forEach(item => {
      item
        .use('sass-resources-loader')
        .loader('sass-resources-loader')
        .options({
          // 單隻檔案引入
          resources: [path.resolve(__dirname, './src/style/mixin.scss')],

          // 多檔案引入
          // resources: ['./path/to/vars.scss', './path/to/mixins.scss']
        })
        .end()
    })
  }
}
```

設定完後，重新啟動`npm run serve`，即可不用個別在component引入mixin.scss，也可以使用@include了

### Reference
- [Vue - 自动化导入](https://cli.vuejs.org/zh/guide/css.html#%E9%A2%84%E5%A4%84%E7%90%86%E5%99%A8)
- [Github Style-resources-loader](https://github.com/yenshih/style-resources-loader)
- [Module build failed: Error: Something wrong with provided resources. Make sure 'options.resources' is String or Array of Strings. ](https://github.com/shakacode/sass-resources-loader/issues/38)