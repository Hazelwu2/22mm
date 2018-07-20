---
title: Rails Server打不開嗎？你應該看看這篇
date: 2018-05-22 03:34:48
tags:
- Rails 5
- close rails server
---
明明Ruby Server已經關閉了，但重新輸入rails server 出現錯誤訊息：Server已經打開了
過去常遇到此問題，我都是直接重開機。後來仔細查詳解，發現有更快的解決方法。

### 出現的錯誤訊息
```
A server is already running. Check /Users/hazelwu/Desktop/github/project/tmp/pids/server.pid.
```

### 除了重開機以外，你還有更好的選擇！

#### 先查出使用的port有什麼應用程式執行
首先要先知道你的Rails Server上一次關閉前是使用哪個port上
預設輸入`rails s`皆為設定port為3000。
例如：`localhost:3000`
3001可以更改為預設的3000，或是看你的server開在哪個port上

在Iterm/commend上輸入下列指令，可以查出3001的port被哪些應用程式開啟。
```
lsof -i tcp:3001
```

[![](https://s3-ap-northeast-1.amazonaws.com/hazel-wordpress/wp-content/uploads/2018/05/22001433/Screen-Shot-2018-05-22-at-12.07.15-AM-1024x158.png)](https://s3-ap-northeast-1.amazonaws.com/hazel-wordpress/wp-content/uploads/2018/05/22001433/Screen-Shot-2018-05-22-at-12.07.15-AM.png)
上圖中可以明顯看到佔用電腦3001的port有`ruby`及`ngrok`
而ruby對應的PID是809

#### 殺掉Ruby對應的PID，強迫關閉
此刻輸入

```
kill -9 809
```
就可以成功將佔用port:3001的Rails Server關閉
重新在command上輸入`rails s -p 3001`即可順利開啟localhost:3001囉

#### 補充：在Cmd如何關閉 Rails Server
除了`Ctrl+C`可以關掉rails server外，如果不小心進入無窮迴圈，用Ctrl+C沒有作用，還有另一種方法：`Ctrl+Z`
1. `Ctrl+C / Cmd + C`
2. `Ctrl+Z / Cmd + Z`


### Reference | 參考資料 
- [Can't stop rails server](https://stackoverflow.com/questions/15088163/cant-stop-rails-server)[![](https://s3-ap-northeast-1.amazonaws.com/hazel-wordpress/wp-content/uploads/2018/05/22001433/Screen-Shot-2018-05-22-at-12.07.15-AM-1024x158.png)](https://s3-ap-northeast-1.amazonaws.com/hazel-wordpress/wp-content/uploads/2018/05/22001433/Screen-Shot-2018-05-22-at-12.07.15-AM.png)