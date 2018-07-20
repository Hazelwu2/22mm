---
title: 遠端ssh連到Server，無法進入Staging Rails Console的終極解決辦法：移除bin
date: 2018-04-30 03:43:52
tags:
- Rails 5
- ssh
- console
- rails server
---
## 情境：
當用ssh遠端連線至server時，要進入專案內的rails console.log，出現rails new [options]，找不到rails project的目錄，紀錄如何解決的筆記。

```
ssh deploy@xxx.xxx.xxx.xxx
cd ~/project/current
bundle exec rails c staging
```
遠端連線至server時，想進去看log訊息，卻出現下圖的錯誤訊息
[![](https://s3-ap-northeast-1.amazonaws.com/hazel-wordpress/wp-content/uploads/2018/04/30233930/14810cce-8bee-4d40-b4de-c877c1371eff.png)](https://s3-ap-northeast-1.amazonaws.com/hazel-wordpress/wp-content/uploads/2018/04/30233930/14810cce-8bee-4d40-b4de-c877c1371eff.png)

```
rails new APP_PATH [options]
....以下略
```

## 解決辦法
專案是用capistrano 1.3.1 版本部署
1. 在local端用力打開 config/deploy.rb
2. 找到linked_dirs，然後把bin吃掉
3. 重新推上github上
4. 重新部署一次
5. 試試看，重新遠端ssh連線，就可以進入staging console囉


### 原始config/deploy.rb檔案設定
```
set :linked_dirs, %w{bin log tmp/pids tmp/cache tmp/sockets vendor/bundle public/uploads public/assets}
```

### 移除bin設定
要移除bin，所以會變成下列的程式碼
```
set :linked_dirs, %w{log tmp/pids tmp/cache tmp/sockets vendor/bundle public/uploads public/assets}
```
接著，重新推到github repo上
重開staging伺服器

```
cap staging deploy:restart
```
大概有九成可以解決問題XD

## Reference | 參考資料
- [rails console command not working
](https://stackoverflow.com/questions/31629983/rails-console-command-not-working)