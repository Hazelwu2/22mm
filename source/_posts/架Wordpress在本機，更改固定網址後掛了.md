---
title: 架Wordpress在本機，更改固定網址後掛了
date: 2017-11-14 06:20:00
tags:
- Wordpress
---
# 架Wordpress在本機，更改固定網址後掛了
## 問題
改完固定網址後就RIP了，天啊Godddd

嗯，在本機上剛成功架完wordpress後很開心的亂試一些功能，忘記應該要開啟git時光機才對。事情是這樣的：我改了固定網址變成： 文章名稱，結果就是點任何後台的連結都變成下載php的檔案.....download資料夾快爆了
<!--more-->

### 解決辦法：
開啟.htaccess檔，找到這兩行刪掉！
`AddHandler php56-cgi .php`
`AddHandler application/x-httpd-php70s .php`
刪掉它，然後重新整理，會發現你的後台復活了。

記得，一定一定要記得`git init`才有保障啊，不然又要除錯到天亮了（？）

## Reference | 參考資料
 [PHP files are downloaded by browser instead of processed by local dev server (MAMP)](https://stackoverflow.com/questions/2316610/php-files-are-downloaded-by-browser-instead-of-processed-by-local-dev-server-ma)