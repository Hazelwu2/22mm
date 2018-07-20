---
title: 教你如何快速設定綁定網域在 Byehost 虛擬主機商
date: 2017-08-25 17:34:27
tags:
- byethost
- wordpress
---

## 前言
申請Byethost免費虛擬主機架站，綁定自己購買的網址
其實研究了有陣子，第一次架站新手，對DNS、子網域很不熟悉。
故記錄起來以防之後有天會忘記XD。
<!--more-->

## 申請Byehost虛擬空間
首先先到Byethost申請虛擬空間，會得到以下的資料及帳號密碼
![byehost虛擬空間](https://i.imgur.com/ScdZjgL.png)
<img class="alignnone wp-image-21" src="http://wualnz.com/wordpress/wp-content/uploads/2017/08/未命名.png" alt="" width="796" height="417" />

## 綁定網址，以Godaddy示範
不想要用Byethost主機預設網址的話？像是`http://你申請的帳號.byethost7.com`
希望別人能直接輸入自己買的網址後，即轉到wordpress部落格或自架網站
而不是醜醜的虛擬主機商贈送的 `http://你的帳號.byethost7.com`


我是購買Godaddy的網址，以下就用Godaddy來示範操作。<a href="https://tw.godaddy.com/" target="_blank" rel="noopener">前往Godaddy 知名主機商</a>
<br>

## 進入Godaddy後台，設定DNS
買好自己中意的網址後，進入Godaddy的後台→網域管理員→旗下的DNS→輸入<b>ns1.byet.org、ns1.byet.org</b>
將自己的網址透過 設定DNS，告訴老爹說你的DNS主機在哪？（自然就是你的虛擬主機空間取得資料）

![byehost虛擬空間 - Godaddy的設定DNS Servers](https://i.imgur.com/uGrDvus.jpg)

還記得剛剛在Byethost註冊的帳號密碼吧？
現在要登入Byethost的cPanel後台，點選Aliases (Parked Domains)選項
![點選Aliases (Parked Domains)選項](https://i.imgur.com/w66Dqun.jpg)

接著，Domain Name輸入你已經申請好的網址
Park onto則是選一開始Byethost主機商預設的網址
設定完後就打工告成啦！
![設定Parked-Domain-Management](https://i.imgur.com/CzLIDq7.jpg)
