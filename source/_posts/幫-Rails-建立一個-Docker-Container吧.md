---
title: 幫 Rails 建立一個 Docker Container吧
date: 2018-04-08 04:15:59
tags:
- Rails 5
- Docker
- webpacker
---
# 幫 Rails 建立一個 Docker Container吧

> ## 為什麼你要使用Docker？
> Docker最重要的價值是：有效解決環境配置、衝突的問題
> 可以在一台機器上跑，就可以在所有的機器上跑。
> 如果有問題的話，就會是大家的問題，大家一起解決XD

docker是建立一個container新的環境，有助於不會因為不同作業系統(Mac, Linux)導致無法在本端跑環境
你不用因為不小心升級了npm的webpack等等........架不起來環境
最後電腦整個砍掉重灌之類的..........相信我你會很想死一千遍QQ。

## Mac 教學
請先安裝 Docker，在Mac環境下安裝Docker非常簡單，只要安裝UI軟體即可XD。
Mac 安裝 Docker = >[請點我下載](https://docs.docker.com/docker-for-mac/install/)

安裝完後，我們先以建立一個新專案來跑Docker
### 建立新專案
首先建立一個新的資料夾，並且先手動建立四個檔案
```
cd desktop
mkdir docker
cd mkdir
touch Gemfile.lock
touch Gemfile
touch docker-compose.yml
touch Dockerfile
```
### 手動填上設定
docker_1可以手動改成自己的專案名稱，ruby版本可以自由更改，撰寫此篇文章時是`Ruby 2.5.0`版本
```
# Dockerfile
FROM ruby:2.5.0
RUN apt-get update -qq && apt-get install -y build-essential libpq-dev nodejs
RUN mkdir /docker_1
WORKDIR /docker_1
ADD Gemfile /docker_1/Gemfile
ADD Gemfile.lock /docker_1/Gemfile.lock
RUN bundle install
ADD . /docker_1
```

```
# Docker-compose.yml
version: '2'
services:
  db:
    image: postgres
  web:
    build: .
    command: bundle exec rails s -p 3000 -b '0.0.0.0'
    volumes:
      - .:/docker_1
    ports:
      - "3000:3000"
    depends_on:
      - db
```
Gemfile最簡單，只要輸入兩行即可

```
# Gemfile
source 'https://rubygems.org'
gem 'rails', '~> 5.1.6'
```
### docker-compose.yml 用途
docker-compose.yml的用途
1. web container
2. database constainer

### 設定完成，跑指令！
設置好檔案後，開始跑指令吧

```
docker-compose run web rails new --force --database=postgresql
```

這個指令輸入後，會自動建立一個容器，裝著新的rails 專案，
Dockerfile會將專案內的Gemfile複製到docker container上，並且自動執行bundle install。
還記得我們剛剛在新增Gemfile添加內容時，只安裝了Rails的Gem

database的username則設定為：postgresql

完整跑完指令後，會發現專案資料夾已經設置好Rails框架了，像是db, assets等等

### 設置database的username
接著來database.yml檔，因為剛剛輸入的指令設定database username是postgresql
因此資料庫的yml檔必須也添加上去，添加`username`、`password`、`host`

```
default: &default
  adapter: postgresql
  encoding: unicode
  # For details on connection pooling, see Rails configuration guide
  # http://guides.rubyonrails.org/configuring.html#database-pooling
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  username: postgres
  password:
  host: db
```

### 架設環境吧！
docker-compose up 的指令等同於 rails s，只是是遠端操作docker，那port設定是在3000
```
docker-compose up 
```
如果設置檔案過程都順利、正常的話，輸入`localhost:3000`即會看到Rails 架設成功的畫面惹。
[![](https://s3-ap-northeast-1.amazonaws.com/hazel-wordpress/wp-content/uploads/2018/04/08232903/Screen-Shot-2018-04-08-at-10.51.52-PM.png)](https://s3-ap-northeast-1.amazonaws.com/hazel-wordpress/wp-content/uploads/2018/04/08232903/Screen-Shot-2018-04-08-at-10.51.52-PM.png)
恭喜你，看到這個畫面時，也代表進入Docker的大坑了（誤）

## Docker 指令介紹
當你在Gemfile 有變動時，都要重跑bundle，那在docker上則是輸入docker-compose build
```
docker-compose build
```
跑任何Rails指令前都必須加上`docker-compose run web`
例如：想要跑rails db:migrate 遷移資料庫指令時前面也要加上此行

```
docker-compose run web rails db:migrate
```

- 開啟伺服器 localhost:3000
`docker-compose up`

- 關閉伺服器 
`docker-compose down`
- 進入Docker rails console
`docker-compose rails c`
## Docker 一次滿足你三個願望
不管你想要安裝Yarn, Node.js還是 Webpacker, 前端Framework都沒有問題唷

### 想在Docker上安裝yarn、Node.js？沒問題

```
RUN apt-get update && apt-get install -y curl apt-transport-https wget && \
    curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - && \
    echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list && \
    apt-get update && apt-get install -y yarn
RUN curl -sL https://deb.nodesource.com/setup_7.x | bash - && \
    apt-get install nodejs
```

### Docker 安裝 Webpacker？沒問題
Docker container上加入`webpacker`？沒問題啊！
在Gemfile上添加`webpacker`

```
gem 'webpacker', '~> 3.4'
```
接著重新Run一次以下指令

```
docker-compose run web bundle update
docker-compose build
```

### Docker上導入React framework？沒問題！
docker-compose run web rails webpack:install:react
你有發現到嗎？其實安裝webpacker指令在rails上也是輸入rails webpack:install
只是前面多加了`docker-compose run web`而已
不要被長長的指令嚇到了Q_Q


### 用 Alias 縮短docker指令
覺得每次都要輸入這史上超長的指令，很浪費人生、手指力氣吧
你想想看，我今天想在docker上新增一個container，我要這樣子輸入
`docker-compose run web rails controller -g home`
超級無敵霹靂有夠長的好不好！

```
vim ~/.bashrc
alias dc-web='docker-compose run web'
alias dc-migrate='docker-compose run web rails db:migrate'
alias dc-rspec='docker-compose run web rspec'
alias dc='docker-compose'
```
以後在終端機上就可以簡單輸入`dc-web`這些囉
也可以自行設定你常用的指令。

