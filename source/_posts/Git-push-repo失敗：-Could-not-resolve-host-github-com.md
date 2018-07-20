---
title: 'Git push repo失敗： Could not resolve host: github.com'
date: 2018-03-01 05:02:26
tags:
- Git
---
今天在練習JavaScript時，正要把成果push到Git repo裡，出現`Could not resolve host: github.com`錯誤訊息，在此紀錄如何解決

## 錯誤訊息
`Could not resolve host: github.com`
## 解決辦法
1. 在終端機上先Ping `github.com`
2. 確認有連線到，Cmd+C結束
3. 直接竄改本地端的host文件
4. 開啟finder -> GOTO -> etc/hosts
5. 將hosts拉到桌面，在127.0.0.1 localhost 下面一行新增「192.30.255.113 github.com」儲存
6. 儲存後將hosts丟回etc/hosts資料夾，會要求輸入admin密碼，輸入後並取代檔案
7. 重新git push origin master，成功