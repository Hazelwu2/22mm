---
title: 安裝Edius時，出現ERROR：Error launching installer解決辦法
date: 2017-07-08 17:55:36
tags:
- Edius
---
# 前言
前陣子安裝Edius時發生了錯誤Error launching installer，爬文Google搜尋，並記錄下來解決方法。
### 問題
安裝Error launching installer時出現錯誤訊息`Error launching installer`
<!--more-->
### 解決辦法
- 把路徑換成別的，把資料夾名稱及含有路徑全改成英文，再重新啟動就可以了

### 原因
- 圖片上資料夾名稱寫edius 7.5軟件，"軟件"為簡體字，導致資料路徑上有特殊符碼(罕見中文或全碼符號)，使得程式無法執行。
- 簡單的解法是把安裝程式移到其他資料夾，如 C:\ ，或是重新命名資料夾名稱，即可啟動執行檔程式進行安裝囉