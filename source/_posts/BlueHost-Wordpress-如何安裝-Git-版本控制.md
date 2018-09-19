---
title: BlueHost Wordpress 如何安裝 Git 版本控制
date: 2018-07-28 23:34:59
tags:
- WordPress
- BlueHost
---
# BlueHost Wordpress 如何使用 Git 版本控制

BlueHost 沒有提供Git或相關Git設定的支援，所以我們只好自立自強，以下安裝教學是在 BlueHost shared 環境下設置。 過程中有參考 BlueHost的官方文件，但是安裝失敗，文件並未寫得很完整，在這篇文章將記錄我是如何安裝 Git 的，以下操作環境為 Mac iOS 10.13.1。

## 為什麼 Wordpress 要進入版本控制？
經營一個專業的 WordPress 網站，有一定的讀者數量，相信有很多痛苦你都知道
以下悲劇範例，我親身經歷過：
1. WordPress 版本升級，A 外掛可能升級後導致和 舊版本的 B 外掛發生衝突，導致網站掛掉。
2. 在 WordPress 後台面板直接修改主題編輯器，常不小心打錯字，導致網站掛掉。
3. 上次改了一些東西，但忘記改在哪裡。這次升級不小心弄掛了，也忘記怎麼改回去了Orz。
4. 在本機上架設一個一模一樣的 WordPress 網站，供測試用。但，在自己電腦修改好了，又要重新到真正網站上重新做一樣的動作，好浪費時間。

通常在WordPress上因為某些套件無法達到你預期想要的效果，於是你興起了客製化的衝動。
客製化會需要更動 php程式碼（包含佈景主題）或 CSS, HTML，一不小心改掛了正式網站也就掛了。

那你可以好好考慮一下學習 Git 來控管你的WordPress，不過前提是你常會更改到程式碼、CSS、HTML。

#### 說了那麼久，到底什麼是 Git 啊？
`Git`，它就是一個遊戲存檔紀錄！
像是玩單機遊戲一樣，你可能玩了幾個小時後便會先存檔，怕下次再玩的時候忘記自己破到哪關了XD。
重新打開遊戲、讀取紀錄檔，又可以回到當初狀態啦！
你每一次更改的東西，Git都會幫你做追蹤。
比如說你今天建立了香蕉、蘋果，你可以分成存成一次的紀錄或兩次。
- 分成一次存檔紀錄訊息：存檔香蕉、蘋果，紀錄訊息：我建立了香蕉、蘋果
- 分成兩次的存檔紀錄：存檔香蕉，紀錄訊息：我建立了香蕉、存檔蘋果，存檔紀錄訊息：我建立了蘋果
然後呢，在你不小心網站爆炸或又被改爆的時候，跟 Git說：嘿！我要回去我那天建立存檔香蕉的紀錄，就會完全復原啦！網站復活YA

> 總結：Git主要功用是幫助你做存檔訊息，網站不小心改爆時還能夠回到當初的存檔紀錄，後面一率掰掰XD
雖然 Git 學習曲線還是有相當程度的高，但若只是使用 存檔、讀取存檔紀錄、查當初改了什麼，那這學習曲線其實很低的，比起大把浪費的時間，嗯...我認為很值得投資。


## 開始之前，您必須具備的條件
#### 能夠 SSH 進入 BlueHost Server 端
SSH KEY 要先進入 BlueHost 後台，先設定好本機電腦的 ssh 公鑰放在上面，id名稱盡量取名為 `id_rsa`
成功設定好後，在終端機上輸入`ssh UserName@ServerIP`即可成功
> 註：UserName、ServerIP 皆在 BlueHost 後台面板上可尋找得到。

你必須先設定好 ssh鑰匙，才能開始下面的奇妙旅程。
#### 需要具備一些 Git 知識
這裡就不多說了，網路上有相當多的教學可以查詢。
根據我們的目的是存檔、讀取存檔紀錄、查當初改了什麼，大部分只會用到下列幾個指令。


git init用意是，和 Git 說：這個資料夾內所有會動的東西，都交給你負責追蹤了！
```
git init
```

Git啊，你幫我存檔 index.php，訊息寫說：「我建立了index.php，邁向里程碑YA!」
（紀錄訊息僅供參考，通常是寫你為什麼做這樣變動，方便回憶，千萬別真的這樣打啊...）
```
git add index.php
git commit -m '我建立了index.php，邁向里程碑YA!'
```

Git 啊，我這個資料夾所有變動的資料，你全部都幫我存檔吧！
```
git add .
git comit -m '存檔所有檔案！'
```

Git 啊，請你幫我回到上一次存檔紀錄吧，我網站改爆了啦>"<
```
git reset HEAD^
```

> 除此之外，還有個圖形化軟體叫：`GitKraken`
它可查看每一次你存檔更動了哪些檔案，對於金魚記憶只有7秒的人類是一大福音，當然對網站管理者來說：再也不用浪費腦容量紀錄這些啦XD
下圖是我其中一個請 Git幫我追蹤的專案，每一次的存檔紀錄 Git 都很認真在紀錄
![GitKraken Example](https://i.imgur.com/kJOqmjk.png)

接下來會教學如何在BlueHost上安裝Git。

## 連線 Server端 ， 安裝 Git
連線進入 Server 後，我們必須先切換家目錄，才能輸入更改 `.bashrc`的設定
```
$ ssh UserName@ServerIP
$ cd
$ pwd
```
用 VIM編輯器開啟 .bashrc
```
$ vim ~/.bashrc
```
接著需要更改.bashrc設定檔，將以下程式碼加入在最後一行。

```
$ export PATH=$HOME/.local/bin:$HOME/.local/usr/bin:$PATH
```
此行用意是指定Git要安裝在$HOME底下的`.local/usr/bin`
既然我們指定了目錄，當然就要創建這些條件囉！
建立資料夾.local，並移動進去。如果你已經有 `.local`資料夾的話，直接進入路徑即可

```
$ mkdir .local
$ cd .local
```
接著再創立一個目錄叫 `src`，並把移動目錄移動到`src`內

```
$ mkdir src
$ cd src
```
如果你在 Commend Line 上輸入 `pwd`的話，目錄應該會長得像是 /home#/username/.local/src，這代表你目前操作都是正確的！
> home為你網站根目錄，username為你ssh連線進入時的使用者名稱，username皆在 Bluehost Panel內可以查詢得到。

接下來，萬事俱備啦！到目前為止，我們先是指定了bashrc（Server端預設是使用bashrc）安裝套件等都安裝在`.local/usr/bin`下，並也真的建立了這些資料夾。

重頭戲就是：安裝 Git 囉（廢話）
我們用`wget`來安裝`git`

```
$ wget --no-check-certificate https://github.com/git/git/archive/master.zip
```
這行指令是在說，向這個網址下載`master.zip`檔案，下載zip檔案後，看到zip當然是要先解壓縮啊！
解壓縮完，並將剛利用完已經失去價值的`master.zip`移除，過河拆巧的概念，節省空間！斷捨離！

```
$ unzip master
$ rm master.zip
```
解壓縮後會出現資料夾，我們先移動到資料夾內，然後開始安裝。

```
$ cd git-master
$ make
$ make install
```
檔案有點龐大，要稍等一下。
最後，測試是否有安裝成功，在終端機上輸入 git
如果你有成功安裝，就應該會跑出這樣的畫面才對喲。

```
usage: git [--version] [--exec-path[=<path>]] [--html-path] [--man-path] [--info-path]

          [-p|--paginate|--no-pager] [--no-replace-objects] [--bare]

          [--git-dir=<path>] [--work-tree=<path>] [--namespace=<name>]

          [-c name=value] [--help]

          <command> [<args>]
```

最後補充一點，雖然 Server已自動安裝 bash-complete 套件了，但 Git 已經習慣打簡略的指令了，可以看看這篇[Git 修改又臭又長的指令，加快開發速度](https://wualnz.com/Git-%E4%BF%AE%E6%94%B9%E5%8F%88%E8%87%AD%E5%8F%88%E9%95%B7%E7%9A%84%E6%8C%87%E4%BB%A4%EF%BC%8C%E5%8A%A0%E5%BF%AB%E9%96%8B%E7%99%BC%E9%80%9F%E5%BA%A6/)
連線至 Server 後輸入以下指令吧！

```
git config --global alias.co checkout
git config --global alias.ci commit
git config --global alias.st status
git config --global alias.br branch
```
他便會自動在 `~/.gitconfig` 紀錄，未來輸入`git commit -m` 就可以直接化簡為 `git ci -m` 啦！
先別聽安麗說了，時間就是金錢，一定要節省時間，不節省時間要幹嘛啊？工程師就是要懶啊XD

### 版本控制
建議版本控制為進入 /public_html內做版本控制，若直接在Home上進版本控制，會收納很多不必要的東西XD。

```
cd
cd /public_html
git init
git st
git ci -m 'Initialize Commit'
```

### 紀錄失敗歷程：官方文件好失敗
[VPS or Dedicated Hosting - Installing Git](https://my.bluehost.com/cgi/help/2383)
照著官方文件做，失敗，無法順利裝上Git，真是的....

```
sudo yum install git
yum install git --disableexcludes=main --skip-broken
```
這兩行指令都失敗QQ，也找不到[官方文件-Enabling Sudo Access](https://my.bluehost.com/cgi/help/2358)上所指的 Access Management。

## Reference | 參考資料
- [Installing GIT on Bluehost Shared Hosting](http://willjackson.org/blog/installing-git-bluehost-shared-hosting)
- [Git 修改又臭又長的指令，加快開發速度](https://wualnz.com/Git-%E4%BF%AE%E6%94%B9%E5%8F%88%E8%87%AD%E5%8F%88%E9%95%B7%E7%9A%84%E6%8C%87%E4%BB%A4%EF%BC%8C%E5%8A%A0%E5%BF%AB%E9%96%8B%E7%99%BC%E9%80%9F%E5%BA%A6/)


