---
title: 攻略：用Jenkins 從 0 到 1 打造強大的前端自動化工作流
date: 2019-12-06 09:49:08
tags:
- Jenkins
- Vue
---
這篇要教導你如何用CI/CD最可靠的工具：Jenkins來打造自動化的前端環境。會有這篇文章是因為公司的專案一直以來都是手動部署的方式，每當更新程式碼要測試時，都需要找有權限的同事幫忙手動部署，一天請同事幫忙五六次都會覺得不好意思。非常希望能夠每次在git push專案時，便會自動抓最新的程式碼並部署在測試伺服器上。

持續整合、持續交付（Continuous Integration & Continous Delivery）簡稱CI/CD，盡量減少手動人力，將一些日常工作交給自動化工具，例如：環境建置、單元測試、日誌紀錄、產品部署。

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
前端專案Vue、後端專案Laravel

## 自動部署做了哪些事？
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
編輯Dockerfile檔，我們會下載jenkinsci/blueocean的image並先安裝nodejs與npm，之所以要客製化是因為我安裝很多次Jenkins的NodeJS Plugin一直失敗，沒辦法跑`npm install`，一直報錯說找不到npm，只好徒法煉鋼，先客製化images，在images除了用現成的jenkins blueocean版本，另外也先裝好nodeJS, npm。

```
# Dockerfile
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
這裡說明一下參數
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
- 打勾 GitHub hook trigger for GITScm polling
- GIthub 點擊專案的Setting，加入 web-hook，設定網址：http://serverIP:8080/github-webhook/，Content Type設定 application/json
- 確保Server 8080的Port有開啟，沒有被防火牆擋住，開8080 Port才能讓流量進來，Github才能Call得到Jenkins

### 設定前後端分離
後端是用Php Laravel框架，前端是用webpack打包成一包dist檔案後，用軟連結映射到後端專案的public/static、/resources/views/index.blade.php，才能順利以view方式顯示

project 是前端專案名稱
backend-project 是後端專案名稱
```
# 直接在server上做設定
ln -s /var/data/workspace/project/dist/static /home/vhost/backend-project/public/static
ln -s /var/data/workspace/project/dist/index.html /home/vhost/backend-project/resources/views/index.blade.php
```

### 在Jenkins上串Salck
接著設定Slack，先在Jenkins安裝Slack外掛
點選「管理Jenins」-> 「設定系統」-> Slack區塊，設定Workspace、Credential
這邊是屬於全域設定的部分，皆會套用到所有Jenkins的專案

![slack](../../../images/Slack.png)
Credential的Kind選Secret text，Secret填Slack給的Token Key，ID和Description可以自由選填
![slack](../../../images/Jenkins-credential.png)
設定成功後，可以試試看點按鈕「Test Connection」，如果成功的話，會在下方顯示Success，且Slack頻道上會有機器人說話
```
Slack/Jenkins plugin: you're all set on http://www.test-jenkins:8080/
```
![Slack](../../../images/Jenkins-Slack-success.jpg)
### 總結
一開始安裝Jenkins是直接用官方印象檔


### Reference
- [持續交付的8條原則](https://samkuo.me/post/2013/11/principles-continuous-delivery/)
- [山姆鍋對持續整合、持續部署、持續交付的定義](https://samkuo.me/post/2013/10/continuous-integration-deployment-delivery/)