---
title: git revert到commit某一版本，Gemfile不會跟著還原，–without development的緣故。
date: 2017-11-11 06:15:41
tags:
- Git
- Rails 5
---
## bundle install -with development 使用注意事項

>某次經驗談：準備將Rails的專案部署Deploy到遠端VPS Server，改檔案改到爛掉於是只好gir revert 還原到上上上上上次的版本，結果囧了發現Gemfile,database.yml不會跟著還原，原本資料庫設定是pg，因要更改production環境時的資料庫變成Mysql2
<!--more-->
### git revert 還原到上上上次版本 使用情境
 - deploy到 Linode，發現掉進無底洞，全部都壞光光
 - production的資料庫預設pg，要更改成Mysql2
 - 修bug修到不知道更改了哪些內容
 - 還原後發現重run bundle install後無法安裝該有的東西
 - 發現是因為曾經下過的指令 bundle install --without    development

突然想起曾經有下過指令是`bundle install --without development`
它非常聰明把你紀錄下來，以後執行bundle install的時候都會附加上–without development

### 下過指令後，要如何取消？
下過bundle install --without development指令後，要如何取消？
- 可以開啟`.bundle/config`就知道了，電腦很聰明的把設定寫在裡面，所以要刪除這個設定就是把它刪掉，刪掉後重新執行bundle install就不會限制任何環境了。

- 或是執行`bundle install  –without development`

### 奇怪？為什麼我 git revert 某版本回去了
輸入rails s 在本機上跑都失敗？
跑出ERROR說我sqlite3的gem沒有安裝成功，請重新安裝
載入無數次的bundle install都一樣，現在終於知道了

 - 先前輸入過指令 bundle install --without development
 - 導致無法完整安裝gem，因為gem的sqlite3寫在development

#### 又因為git revert到很前面的版本，導致git push的時候會出現錯誤訊息

`Updates were rejected because the tip of your current branch is behind`

#### 良心建議
git pull然後一個一個慢慢解決衝突版本
不過這專案只有我一人在做，在那邊pull真的是會拉到死又浪費時間，只好使出大絕招

`git push -u origin master -f `

強制push上去蓋掉前面的紀錄，不過會導致遠端的git res會有些變動遺失就是，要用請謹慎小心。

