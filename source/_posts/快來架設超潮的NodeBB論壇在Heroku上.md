---
title: 快來架設超潮的NodeBB論壇在Heroku上
date: 2018-12-10 23:10:09
tags:
- node.js
- forum
- heroku
---
# 架設 NodeBB 論壇 在 Heroku 上
This tutorial is introduce how to install NodeBB forum with MongoDB on MacOS and how to create in Heroku application.
I look up official document, and step by step do it, but has some error. I'm spending much more time to fixing them. 
So I write this topic. I hope this tutorial can help somebody.

架設Nodebb論壇照著[官方文件]((https://docs.nodebb.org/installing/cloud/heroku/)的步驟一步步做，但仍出現一些狀況無法順利架設在 Heroku上，因此寫下筆記記錄下來。

下列教學總共會分成幾個步驟

1. 將NodeBB原始碼下載到電腦上
2. 架設環境在本機上
3. 建立專案在Heroku 上
4. 設定專案連接到 MLab （Mango）資料庫

## 架設環境
- 架設環境 為Mac 10.14 OS

## 安裝前必備品
下列將列出如何安裝 NodeBB在本機環境下，且架設在heroku上，請先確保您有以下環境、知識。
1. 安裝 [Heroku Cli](https://toolbelt.heroku.com/)，可以在終端機執行像`heroku create`、`heroku login`等指令。
2. 具備基本的 Github 知識
3. Mac 已有安裝 HomeBrew
4. Heroku 已擁有帳號


## 開始安裝
### NodeBB 下載到本機
Download NodeBB to your localhost.
首先先把專案載入到電腦本機上，開啟終端機並移動到桌面，輸入
```
git clone https://github.com/NodeBB/NodeBB.git
cd NodeBB
```
便會在你的桌面上建立一個NodeBB資料夾，裡面便是原始碼。

### 安裝MongoDB
若你已有安裝，可跳過這步驟。
若你系統尚未有[HomeBrew](https://brew.sh/index_zh-tw)套件管理工具，請移駕至Homebrew官網下載並安裝。

```
brew install mongodb
```
安裝完以後，系統並不會直接開啟，需要輸入以下指令
```
brew services start mongodb
```
輸入後，mongodb才能在系統上使用哦！

### Heroku 建立專案
首先先確保你有安裝Heroku Cli，若沒有請先Google查詢先安裝，否則下列指令皆無法執行。

我們先在終端機上輸入以下指令，登入自己的heroku帳號
```
heroku login
```
接著會提示你輸入帳號及密碼，輸入完沒有打錯的話，便會成功登入。
接著在Heroku上直接建立專案，nodebb-forum可以自行取名，或著直接打heroku create也可以，heroku便會隨機幫你建立一個名稱。
```
heroku create twlesmatches-forum
```
建立好專案後，我們要去設定MLab MongoDB 與自己的heroku帳號 建立連結，可以先選擇SendBox的免費專案
[MLab建立連接](https://elements.heroku.com/addons/mongolab)
點擊上列連接，並找到按鈕「Install mLab MongoDB」進入頁面
![Picture install mLab Mongo DB](https://i.imgur.com/EaecwAu.png)。
他會要求你登入帳號，並選擇要連接的專案。
像我剛剛在heroku上建立專案叫twlesmatches-forum，便在這個視窗輸入tw，便會跳出來囉，選好專案後便點擊按鈕「Provision add-on」即可加入至專案。
![Find project](https://i.imgur.com/H5BY7Rt.png)
或是比較省事一點，直接在終端機輸入以下指令，並選擇sandbox的免費專案。
```
heroku addons:create mongolab:sandbox
```

### NodeBB 與 Mongo資料庫 連接
成功連接後，我們便來設定與資料庫連接
在終端機上輸入以下指令，請記得必須要在NodeBB的根目錄才行
```
./nodebb setup
```
如果輸入setup指令後，出現ERROR訊息：
`error: NodeBB could not connect to your Mongo database. Mongo returned the following error: Authentication failed.`
這段Error訊息，是說專案沒辦法連接本機上的Mongo，是因為權限不夠高的關係。

請在終端機上輸入以下指令，進入mongo資料庫的介面
```
mongo
```
如果無法順利進入mongo的介面，請先檢查是否有安裝成功mongoDB或著是`brew service restart mongo`，將服務重啟。

順利進入介面後，請再輸入以下指令
便可成功切換到nodebb的資料庫
```
> use nodebb
switched to db nodebb
```

### Mongo 簡單指令
mongo介面下列有幾個指令可以檢查這個nodebb資料庫的使用者
``` bash
db.getUsers() # 查詢所有使用者
show roles; # 秀出所有的權限角色，像readWrite, userAdmin
```

輸入上面的指令像是db.getUsers()會發現，nodeBB資料庫內沒有任何一個使用者，所以我們要建立一個使用者，並且有足夠高的權限可以操作，角色為userAdmin。

我們在終端機上輸入以下指令，在輸入前，請將`username`, `password`替換成你想要設定的名稱及密碼，這組設定將會是你本機上連接資料庫時的帳號及密碼，要記得！
```
db.createUser({user:'username', pwd:'password', roles:[{role:'userAdmin', db:'db-name'}]})
```
若想要設定其他權限，可以輸入`show roles;`，查詢資料庫內其他角色的權限，再輸入一次上述指令，將`role: 'userAdmin'`改成其他你想要的role即可。

設定好之後，並重新再跑一次setup指令
```
./nodebb setup
```
![runing setup](https://i.imgur.com/6q8KRLi.png)
輸入後他會跑一下子，接著會要求你設定一些選項，這邊非常重要
關係到你能不能架設在Heroku上連接資料庫成功，所以輸入時要提高注意力一下XD，不能全部都按Enter！！
（我第一次架失敗時就是全部都按Enter XD）
``` bash
URL used to access this NodeB(http://localhost:4567) https://twlesmatches-forum.herokuapp.com/
```
首先要設定的是URL，請在這裡填上你的heroku application的url，不知道的話請開新的終端機視窗輸入`heroku open`便會跳出你專案的網址，格式會像是這樣：`https://twlesmatches-forum.herokuapp.com/`


接著要你設定 NodeBB secret，不用理她，Enter跳過！
``` bash
Please enter a NodeBB secret (ca7c68c3-edce-4a7d-80f8-7e5e757ca5ca)
```

接著會詢問你要使用的Database是什麼？由於NodeBB只允許Mongo或Redis這兩款資料庫可使用，我們前面辛苦安裝Mongo了，就直接Enter吧XD。（Enter代表輸入他預設的指令）
``` bash
Which database to use (mongo)
```
接著會出現重頭戲！這個NodeBB要連接的資料庫網址是什麼？也有出現提示格式如何輸入。
``` bash
2018-12-10T14:52:49.320Z [14213] - info:
Now configuring mongo database:
MongoDB connection URI: (leave blank if you wish to specify host, port, username/password and database individually)
Format: mongodb://[username:password@]host1[:port1][,host2[:port2],...[,hostN[:portN]]][/[database][?options]]
```
這很簡單，開啟Heroku網站，並登入帳號後選擇你剛剛建立的application專案，進入專案後到Setting找到Config Vars，就有資料庫網址了！把他複製貼上到終端機上即可囉。（也可直接輸入heroku config，便會出現資料庫的網址）
![示意圖](https://i.imgur.com/QospqAE.png)
輸入完後，便會自動跑完安裝啦
![tutorial for nodebb and heroku](https://i.imgur.com/JtTVSQF.png)
跑完安裝後要注意，會有個提示訊息告知你的Admin系統管理員帳號及密碼是多少，不要忽略了哦

接著剩下最後兩個步驟了，快大功告成啦！
請在終端機輸入以下指令，建立Procfile在Heroku上
```
web node app.js --no-daemon
```
教學文件是打loader.js，但照著打反而出錯，爬文後發現打app.js才能成功架設。
輸入完後，請git commit做紀錄後推上去。
```
git add -f Procfile config.json package.json build && git commit -am "adding Procfile and configs for Heroku"
git push heroku master
```
推上去heroku master要稍等一會兒時間。
推完後，請再輸入終端機，將dyno設定為1（free）的等級
```
heroku ps:scale web=1
```

大功告成，快上去看看架設成功了沒！輸入下列指令會自動開啟專案的網站哦
``` 
heroku open
```

如果上述教學有任何錯誤或是需要糾正的地方，請務必留言告訴我，謝謝各位^_^
## Reference
- [官方文件：install nodebb in heroku](https://docs.nodebb.org/installing/cloud/heroku/)
- [Install NodeBB in Heroku](http://zhangwenli.com/blog/2016/04/19/installing-nodebb-on-mac-os/)
- [Heroku 運行類別、 Procfile、常用指令筆記](https://andyyou.github.io/2016/10/31/process-types-and-profile/)
- [NodeBB 基礎使用與開發](https://www.kancloud.cn/a632079/nodebb-cn/content)

