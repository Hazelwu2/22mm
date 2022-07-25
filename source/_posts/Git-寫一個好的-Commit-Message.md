---
title: '[Git] 寫一個好的 Commit Message'
date: 2021-10-18 14:47:18
tags:
- git
---

- 區分標題(Subject)與內容(body)，用一個斷行符合隔開

## Commit 分類
- feat：功能，例：feat: 完成登入功能
- fix：bug，例：fix: 修復登入頁面顯示錯誤
- hotfix: 緊急，例：hotfix: 緊急修復無法登入
- docs: 文檔，例：docs: 修改 README
- style: 不影響程式碼的前提下修改，像是空格、格式、斷行、缺少分號等。例：style: 刪除空格、斷行、刪除 console
- perf: 提高性能程式碼
- test: 添加缺少測試或糾正現有測試，例：test: 添加登入頁測試
- build: 修改打包的部分，例：build: 修改 vercel 打包設定
- ci: 修改 CI 的設定，例：ci: 修改 drone.yml 檔
- refactor: 重構程式碼，不修改錯誤、功能。例：refactor: 添加 .gitignore
- revert: 還原到之前的紀錄。例: revert: #62

## 多行 commit 
git commit -m 用單引號來換行
```
git commit -m '
1. feat: 完成註冊切版
2. feat: 完成註冊串接API
'
```


## Reference
- [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/)
- [git规范](https://juejin.cn/post/6844904014983725064)
- [GIT如何使用 git commit -m 命令写多行注释的方法](https://blog.csdn.net/qq_37858386/article/details/82768598)
- [TIL About Adding a New Line to "git commit -m"](https://www.rockyourcode.com/til-about-adding-a-new-line-to-git-commit-m/)