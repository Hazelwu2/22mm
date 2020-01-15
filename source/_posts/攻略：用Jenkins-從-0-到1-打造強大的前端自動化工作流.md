---
title: 攻略：用Jenkins 從 0 到 1 打造強大的前端自動化工作流
date: 2019-12-06 09:49:08
tags:
- Jenkins
- Vue
---
這篇要教導你如何用CI/CD最可靠的工具：Jenkins來打造自動化的前端環境。會有這篇文章是因為公司的專案一直以來都是手動部署的方式，每當更新程式碼要測試時，都需要找有權限的同事幫忙手動部署，一天請同事幫忙五六次都會覺得不好意思。非常希望能夠每次在git push專案時，便會自動抓最新的程式碼並部署在測試伺服器上。

持續整合、持續交付（Continuous Integration & Continous Delivery）簡稱CI/CD，盡量減少手動人力，將一些日常工作交給自動化工具，例如：環境建置、單元測試、日誌紀錄、產品部署。

如果沒有 `CI/CD`，前端要更新程式碼的工作流程會是這樣

1. 本地端寫程式
2. 提交程式碼，Push到Git Reporistory
2. 提交程式碼，Push到Git Reporistory
3. 終端機輸入npm run test:unit，看單元測試結果
4. 存檔程式碼，Git Commit後並Push到Github Reporistory
5. 連線到測試伺服器，移動到專案，git pull下來、執行npm run build


有了CI/CD後，整個流程就會變成像下面這樣
1. 本地端寫程式
2. 

## 專案用了哪些工具
- Git 版本控制
- GitHub 程式碼託管、審查
- Jenkins 自動化建置、測試、部署
- Docker 可攜式、輕量級的執行環境
- Slack 團隊溝通、日誌、通知

## 看完這篇你能得到什麼？
- 實際操作，瞭解CI/CD的好處
- 瞭解如何設定 Jenkins 在前端 Vue 的專案上

## 專案環境
以下的專案環境是用CentoOS Linux 7.7.1908做安裝及設定
前端專案Vue、後端專案Laravel，專案有設定前後端分離，也就是前端一包專案、後端一包專案，後端只產生API給前端接
Vue專案及Jenkins都安裝在測試主機上，並沒有個開
## 自動部署做了哪些事？
自動部署會先去Github將專案最新程式碼拉下來，並開始安裝套件`npm install`，接著再用webpack打包檔案`npm run build:prod`
打包完會產生一包`dist`資料夾，dist資料夾會對應到後端專案的MVC裡的View。

```
npm cache clean -force
npm install
npm run build:prod
```


## 步驟
我會分成下面幾個步驟
0. Centos 安裝 Docker 
1. 用 Docker來安裝 Jenkins
2. 設定Github hook時觸發 Jenkins
3. 設定前後端分離
4. Jenkins和Slack串接，當Jenkins部署時，Slack會發出通知

### Centos 安裝 Docker
先在Centos卸載舊版本，以避免後續的錯誤
```
sudo yum remove docker \
  docker-client \
  docker-client-latest \
  docker-common \
  docker-latest \
  docker-latest-logrotate \
  docker-logrotate \
  docker-engine
```
接著再來安裝 Docker
```
sudo yum update
sudo yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2
sudo yum-config-manager \
  --add-repo \
https://download.docker.com/linux/centos/docker-ce.repo
sudo yum install docker-ce
```
啟動 Docker
```
sudo systemctl start docker
```
設定每次開機都會自動啟動 Docker
```
duso systemctl enable docker
```
### 建立客製化DockerFile
新增一個Dockerfile檔在伺服器的/var/dockerContainer資料夾
```
cd /var/dockerContainer
touch Dockerfile
```
編輯Dockerfile檔，我們會下載jenkinsci/blueocean的image並先安裝nodejs與npm，之所以要客製化是因為我安裝很多次Jenkins的NodeJS Plugin一直失敗，沒辦法跑`npm install`，一直報錯說找不到npm，只好徒法煉鋼，先客製化images，在images除了用現成的jenkins blueocean版本，另外也先裝好nodeJS、npm。

``` Dockerfile
# 從 DockerHub 安裝 Jenkinsci/blueocean image。
FROM jenkinsci/blueocean

# 指定 User 是 root，有最大權限。
USER root

# 設定 Container 的預設目錄位置。
WORKDIR /var/jenkins_home/workspace/project

# 安裝 NodeJS、Npm。
RUN apk add --no-cache --update nodejs npm
```
編輯好Dockerfile並存檔，我們先來build image
```
docker build -t frontend-ui-image .
```
成功跑完後你可以輸入`docker images`會看到frontend-ui的images檔，接著利用它為基底，啟動container
```
docker run -d
  --name frontend-ui \
  -p 8080:8080 \
  -u root \
  -e JAVA_OPTS=-Dorg.apache.commons.jelly.tags.fmt.timeZone=Asia/Taipei \
  -v /var/data:/var/jenkins_home \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v "$HOME":/home \
  --restart=always \
  frontend-ui-image
```
以下將說明參數的用途
- --name frontend-ui：容器名稱命名為frontend-ui
- -p 8080:8080：映射出8080 Port與容器裡的8080 Port 相應
- -u root：以User root身份執行，否則會沒有權限
- -e JAVA_OPTS=-Dorg.apache.commons.jelly.tags.fmt.timeZone=Asia/Taipei，設定環境變數時區，沒有設定此選項，會導致Jenkins的時間不是UTM+8
- -v /var/data:/var/jenkins_home：容器的儲存空間/var/jenkins_home 此目錄會映射到容器外的/var/data資料夾
- --restart=always：當容器遇到例外的情況被STOP掉，例如是重新開機，Docker 會試著重新啟動此容器
- frontend-ui-image：用Images frontend-ui-image啟動容器

順利啟動容器後，打開伺服器網址：xxx.xxx.xxx.xxx:8080，便會看到Jenkin成功畫面
![Jenkins](../../../images/jenkins-success.png)

進入Jenkins後，請先安裝Jenkins預設外掛
![Jenkins Install](../../../images/jenkins-install.png)
### 設定 Github Hook 觸發 Jenkins
接著我們要來設定每次Git Push時會觸發專案啟動Jenkins，在設定前我們需要先新增專案
Jenkins點擊新增作業，選取「建置 Free-Style 軟體專案」

![Jenkins 建置 Free-Style 軟體專案](../../../images/jenkins-free-style.jpg)

選好之後點OK進入到下一步，接著近一步細部設定，原始碼管理區塊要設定專案網址，並設定Github的帳號密碼，讓Jenkins有權限可以讀取專案。
在建置觸發程序要打勾「GitHub hook trigger for GITScm polling」，當GitHub Repository有變動時會自動通知 Jenkins 來進行編譯
![Jenkins 原始碼管理、建置觸發程序](../../../images/jenkins-step3.jpg)

除了設定Jenkins要進行編譯之外，Github Repository這端也要設定 Webhook
- Github 點擊專案的 Setting，加入 web-hook
Payload URL 網址設定為你的Jenkins Server 網址，例：http://serverIP:8080/github-webhook/，Content Type 要設定 application/json
Secret設定為空沒有關係，另外有詢問你什麼時候要觸發這個webhook，在圖片上是設定當只有push 事件發生時，才會觸發，當然你也可以客製化
- Jest the push event：只有push事件發生才會觸發 webhook
- Send me everything：不管發生什麼事，都會觸發 webhook
- Let me select individual events.：選取這個可以客製化哪些event才要觸發

![加入webhook設定](../../../images/webhook-add.jpg)
設定完會像下面這張圖片一樣
![webhook](../../../images/webhook.jpg)
- 確保Server 8080的Port有開啟，沒有被防火牆擋住，開8080 Port才能讓流量進來，Github才能Call得到Jenkins


### 設定前後端分離
Web Server是採用nginx，後端是用Php Laravel框架，前端是用webpack打包成一包dist資料夾後，用軟連結映射到後端專案的public/static、/resources/views/index.blade.php，才能順利以view方式顯示，同時也可以解決前端打後端專案的API有CROS跨網域問題。

project 是前端專案名稱
backend-project 是後端專案名稱
```
# 直接在server上做設定
ln -s /var/data/workspace/project/dist/static /home/vhost/backend-project/public/static
ln -s /var/data/workspace/project/dist/index.html /home/vhost/backend-project/resources/views/index.blade.php
```

nginx 設定
```
# /etc/nginx/conf.d/project
server {
    listen 80;
    listen [::]:80;
    root /home/vhost/backend-project/public;
    index index.php index.html index.htm;

    server_name xx.xx.xxxx.xx;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }
    location ~ \.php$ {
        fastcgi_pass   127.0.0.1:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }
}
```


### 在Jenkins上串Salck
接著設定Slack，先在Jenkins安裝Slack外掛
點選「管理Jenins」-> 「設定系統」-> Slack區塊，設定Workspace、Credential
這邊是屬於全域設定的部分，皆會套用到所有Jenkins的專案

![slack](../../../images/Slack.png)
Credential的Kind選Secret text，Secret填Slack給的Token Key，ID和Description可以自由選填
至於Token Key可以從Slack 的Jenkins頁面拿到
![Token](../../../images/jenken-slack-token.jpg)


![slack](../../../images/Jenkins-credential.png)
設定成功後，可以試試看點按鈕「Test Connection」，如果成功的話，會在下方顯示Success，且Slack頻道上會有機器人說話
```
Slack/Jenkins plugin: you're all set on http://www.test-jenkins:8080/
```
![Slack](../../../images/Jenkins-Slack-success.jpg)

## 總結
一開始安裝Jenkins是用Docker Images: jenkins，預設外掛都不能安裝，才發現要裝 Images: jenkins/jenkins，而不是jenkins。
用Images jenkins/jenkins啟動容器後發現第二個問題是容器內沒有node.js，裝了外掛NodeJS仍無法解決我的問題，最後還是自製Images比較快。
不過CI/CD還是很單調的流程，還沒有到很複雜，至今也還在學習中，如果有問題或是有寫錯的地方，歡迎下面留言喔

- [X] jenkins：images版本過舊
- [X] jenkins/jenins：沒有nodeJS，Jenkins 外掛 NodeJS也無法使用
- [X] jenkinsci/blueocean：沒有nodeJS，Jenkins 外掛 NodeJS也無法使用
- [O] frontend-ui-image：自製images，jenkinsci/blueocean + 自行安裝nodeJS

之所以後來選擇images是採用jenkinsci/blueocean的版本，也是看上它有很漂亮的UI、使用者體驗佳，至今還在摸索中，如果有更多的心得會再多寫一篇文章分享，謝謝看到這裡
![Jenkins BlueOcean Snapper](../../../images/jenkins-blueocean.jpg)

## Reference
- [Centos Install Docker Document](https://docs.docker.com/install/linux/docker-ce/centos/)
- [持續交付的8條原則](https://samkuo.me/post/2013/11/principles-continuous-delivery/)
- [山姆鍋對持續整合、持續部署、持續交付的定義](https://samkuo.me/post/2013/10/continuous-integration-deployment-delivery/)
- [永久修改以容器化方式运行的Jenkins系统时间](https://www.jianshu.com/p/47d767cf893d)
- [Introducing Blue Ocean: a new user experience for Jenkins](https://jenkins.io/blog/2016/05/26/introducing-blue-ocean/)
- [前端开发如何让持续集成 / 持续部署 (CI/CD) 跑起来](https://juejin.im/entry/5a8f919af265da4e8e787606)
