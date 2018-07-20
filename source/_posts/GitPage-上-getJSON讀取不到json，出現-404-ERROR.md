---
title: GitPage 上$.getJSON讀取不到json，出現 404 ERROR
date: 2017-11-05 06:17:18
tags:
- Github Pages
- Git
---
# GitPage 上$.getJSON讀取不到json，出現 404 ERROR
我將content.json檔放在Localhost測試正常後，才push到github，接著開啟GitPage，發現沒辦法實行AJAX功能，讀取不到我的content.json資料

### Consolo.log顯示
- `Failed to load resource: the server responded with a status of 404 ()`

### 什麼原因造成的？
- Github pages is loaded over https and your API request is over http, which is not allowed. You are serving the site over HTTPS but trying to load jQuery over HTTP. This is not allowed.
### 除錯過程
1. 檢查.gitinore是不是忽略到我的json檔
2. 將路徑$.getJSON改成URL絕對路徑
  - ``` $.getJSON('../data/content.json', function)```
  把裡面的`../data/content.json`改成
  - `https://hazelwu2.github.io/AjaxMasonryPhoto/data/content.json`
  
重新更改路徑後，Gitpage就抓得到我的資料了。
初步判斷應該是因為HTTPS，但上傳到github是http，導致無法拿到資料。

如果有問題、筆誤或寫錯，歡迎糾正我，謝謝
  
### 參考網站
- [這可憐的老兄遇到的問題和我一樣](https://forum.freecodecamp.org/t/cant-receive-json-with-getjson-or-ajax-when-publish-on-github-pages/72438/10)
- [CROS-anywhere](https://github.com/Rob--W/cors-anywhere)
- [Reading Json file from github pages server using javascript](https://stackoverflow.com/questions/44279919/reading-json-file-from-github-pages-server-using-javascript)