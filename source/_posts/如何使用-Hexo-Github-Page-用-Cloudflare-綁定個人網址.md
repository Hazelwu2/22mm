---
title: 如何使用 Hexo + Github Page 自訂網域名稱 + Cloudflare SSL 免費憑證 
date: 2018-07-08 15:26:50
tags:
- Hexo
- CloudFlare
---
# 如何使用 Hexo + Github Page 用 Cloudflare 綁定個人網址
![如何使用 Hexo + Github Page 用 Cloudflare 綁定個人網址](https://i.imgur.com/NDKQqjz.png)
本篇適用於用Hexo框架架設在Github Pages上，但想要綁定自己買的網址。
由於Github Pages 產生的個人網址太長，對 SEO 不友善，不是這麼好被搜尋引擎收錄，

## 步驟
1. Github Repo 設定 Custom Domain
2. 在 Repo專案下新增 CNAME，完成網址綁定
3. 用 Cloudflare 提供免費的 SSL 憑證，得以正確讀取 CSS、Google視為安全網站認證

## Github 設定專屬網址
開啟Github的Hexo專案，選Setting->Options-> Github Pages -> Custom Domain，填入自己的網域（例如：`wualnz.com`），接著儲存。
![Setting Github Custom Domain figure](https://i.imgur.com/7ZyGcn5.png)

### 新增 CNAME
在該 Hexo 專案下的 `source` 資料夾下新增檔案 `CNAME`
```
## In project
cd /source
touch CNAME
```
修改 CNAME 內容，將域名填入即可，並重新部署到Github。
```
  wualnz.com
```
[點我查看 Github Repo CNAME 設定完成範例](https://github.com/Hazelwu2/22mm/blob/master/CNAME)

> 到底要放在 `/source` 還是 `最上層資料夾`？

若使用Hexo框架的話，要將`CNAME`放置在 `/source`資料夾內，原因是：當每次`hexo deploy`時才不會被覆蓋到。
若不是使用Hexo的話，沒有自動部署，直接將`CNAME`檔放在Proejct內即可。


###  CNAME 導向個人網址 原理 

使用者在瀏覽器輸入`wualnz.com`後，DNS 便會開始解析找到 Github的 IP
<br>

Github上有成千上百萬個Pages由不同使用者建立，Github不會知道網址`wualnz.com`是屬於 哪個 User 哪個 Repo，因此需要在Repo下放上 CNAME，這樣 Github才找得到，哦！就是這個hazelwu2/22mm的Repo！並回傳給瀏覽器

#### CNAME 是什麼？

它是一種 DNS 紀錄類型，可以將別名（綽號）對應到實際或正規的網址。
當你在瀏覽器上輸入 `wualnz.com`時，瀏覽器會去找 `DNS` ，經過DNS的解析 `wualnz.com` 得到： `wualnz.com` Github IP 位址，例如找到的是`192.168.0.444`。

> 最常見的 `CNAME` 用法
瀏覽器上輸入 `www.wualnz.com` Enter後會自動跳轉到（對應到）`wualnz.com`
不論是輸入`wualnz.com`還是`www.wualnz.com` 都能正確導向至網站。

#### A Records 是什麼？
A Records 就是 該主機的 IP 位址。
像 `wualnz.com` 方便人們記憶的名稱，最後會被解析成很難記住的 IP 位址，例：`192.168.0.444`

## Hexo 設定 url 個人網域
打開_config.yml
其中找到url與root，設定為自己網域名稱。

若沒有個人網域，可以用預設的Github Pages產生的url（例如：`hazelwu2.github.io/22mm`)

### 無個人網域 設定
```
## _config.yml
url: http://github帳號.github.io/repo名稱
root: /repo名稱
```

舉例來說，我的Github帳號是`hazelwu2`，Repo名稱叫`22mm`，那麼設定就會像下列這樣
```
## _config.yml
url: https://hazelwu2.github.io/22mm/
root: /22mm/
```

### 有個人網域 設定
有個人網域的話，開啟 _config.yml，並將url設定成自己購買的網址，記得`/`不要省略
```
url: http://wualnz.com/
root: /
```
最後，再回到github你的repo內，設定 Custom Domain

1. 進入github，點 Hexo 的 Repo，並進入設定
2. GitHub Pages 下的 Custom domain
3. 修改成你購買的網域。（要與CNAME檔案設定一致）

（例：我的網域是wualnz）
![Github 設定 Custom Domain Hexo](https://i.imgur.com/kbEuK16.png)


以上步驟完成後，便綁定個人網域成功。
<br>

## Hexo 綁定自訂網址後，CSS 無法正常載入
綁定網址後開心的進入網站，發現：靠～無法載入 CSS ，打開 Console.log 檢查發現 
```
404 Error Not found
```
> 原因來自於：
Github Page 不支援自定義的 SSL 憑證，也就是說：使用Github Pages 綁定自己的網址，會和 Github 起衝突
### 解決方法
1. 自訂網域付費購買 SSL 憑證
2. 使用免費憑證（例：Let's Encrypt）
3. CDN Cloud Flare服務

比較建議使用 CDN 的 Cloud Flare，Let's Encrypt雖是免費憑證，但需要三個月手動更換一次。
接下來會介紹如何在 Cloudflare 做設定

### Cloudflare 設定
設定前，需要將你的網址商設定`網域名稱伺服器`為Cloudflare提供的網址，讓Cloudflare 代管 DNS Server
我買的網址商是Godaddy的，因此在Godaddy面板上設定會像是這樣
![Godaddy 設定 Cloudflare 代管DNS](https://i.imgur.com/ad5nlWf.png)
在網址商Godaddy設定成功後，在Cloudflare樣板會看到 Status:Active，表示設定成功。
![Cloudflare Setting DNS Server Active Successful](https://i.imgur.com/p497z5I.png)

前往Cloudflare DNS
1. 新增兩個 A Records，皆指向Github IP：`192.30.252.153`、`192.30.252.154`
2. 新增 CNAME，NAME 填 www，Value填上 Github帳號.github.io
設定完成後，應該會像下圖這樣。
![Cloudflare setting github page with custom domain](https://i.imgur.com/M2QuOzg.png)

### 設定 SSL 憑證
Cloudflare DNS 設定完成後，到Crypto標籤，我們要設定 SSL憑證

SSL有Flexible、Full選項，這兩個都可以選。
![Cloudflare Crypto setting SSL](https://i.imgur.com/4IRZ3Ic.png)
將 Always user HTTPS 打開，變成 On狀態
![Cloudflare Crypto setting SSL](https://i.imgur.com/lqmzqKD.png)

設定完後，恭喜你，重新Refresh網址後，網站已經能成功抓到 CSS 囉

> #### Cloudflare SSL Flexible 、 Full 差別
下圖為[官方網站提供的說明圖樣](https://support.cloudflare.com/hc/en-us/articles/200170416-What-do-the-SSL-options-mean-)
Flexible、Full 差別在於 Cloudflare 與 Server 間 **是否為加密連線**，官方推薦大家設定 Full SSL。
Flexible 是 對 Server 間的連線是不加密的。
![Cloudflare SSL Flexible](https://support.cloudflare.com/hc/en-us/article_attachments/206124658/cfssl_flexible.png)
Full 是你擁有一方的SSL憑證，你可以自由選擇一方是否加密。白話一點就是：CloudFlare不管是對訪客、還是對Server端都能夠加密！而且免付費會員也可以使用這功能。
![Cloudflare SSL Full](https://support.cloudflare.com/hc/en-us/article_attachments/206167937/cfssl_full.png)

如果想知道更詳細內容，可以看看我寫的這篇文章：關於[Rails & Cloudflare use Free SSL, 無法登入後台 解決辦法](https://medium.com/@hazelwu/rails-%E8%97%89cloudflare-%E5%85%8D%E8%B2%BBssl%E8%AA%8D%E8%AD%89%E5%BE%8C-%E7%84%A1%E6%B3%95%E7%99%BB%E5%85%A5%E5%BE%8C%E5%8F%B0-%E8%A7%A3%E6%B1%BA%E8%BE%A6%E6%B3%95-4b87d59be765)


## Reference

- [Hexo+GitHub+Cloudflare实现自定义域名全站SSL](https://liyang.pro/hexo-github-cloudflare-customer-domain-ssl/)
- [What do the SSL options mean?](https://support.cloudflare.com/hc/en-us/articles/200170416-What-do-the-SSL-options-mean-)