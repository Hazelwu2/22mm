---
title: 'warning: LF will be replaced by CRLF in files？這什麼東西啦！'
date: 2017-11-14 06:13:59
tags:
- Git
---
有沒有遇過git add加入索引時，突然跳出這樣的訊息？
> warning: LF will be replaced by CRLF in files.

我遇到的情境是這樣的，咳：
在Wordpress進入`git init`版本控制後，
出現一大堆奇怪的字樣，如果說是線上遊戲的公頻，它整個就是把我的iTerm2徹底大洗版啊啊啊！！！！一直廣播是怎樣

```
warning: LF will be replaced by CRLF in files.
The file will have its original line endings in your working directory.
warning: LF will be replaced by CRLF in files.
The file will have its original line endings in your working directory.
warning: LF will be replaced by CRLF in files.
The file will have its original line endings in your working directory.
warning: LF will be replaced by CRLF in files.
The file will have its original line endings in your working directory.
warning: LF will be replaced by CRLF in files.
The file will have its original line endings in your working directory.
```

做了一些功課才知道
### 什麼是LF？
它是ASCII 10電腦編碼系統，換行符號：`\n`
簡單無腦的來說`LF = \n`
### 什麼是CRLF？
ASCII 13的電腦編碼系統，他和LF比較不一樣的是多了`\r` 
沒錯，它就是用`\r\n`進行換行的，

#### 既然`\n`就能換行，何必多浪費兩個字元`\n\r`來換行？
節省空間嘛！Git很聰明的想要節省空間，紀錄檔案時對於換行都是採\n的方式
所以釋出這訊息是為了要讓工程屍知道一下（？）善意的提醒。

#### 原本檔案是CRLF，Git轉換成LF，那會有什麼影響嗎？
在unix系統上，若從git上拿到檔案，程式碼就永遠是LF格式了，再也回不去CRLF了。

### 關閉警告指令
若是覺得沒有關係，想關閉這個警告訊息輸入下列指令就好了。
`git config core.autocrlf true`

###  Reference | 參考資料
- [LF will be replaced by CRLF in git - What is that and is it important? ](https://stackoverflow.com/questions/5834014/lf-will-be-replaced-by-crlf-in-git-what-is-that-and-is-it-important)
- [Git 在 Windows 進行 add 出現警告「warning: LF will be replaced by CRLF](https://shazi.info/git-%E5%9C%A8-windows-%E9%80%B2%E8%A1%8C-add-%E5%87%BA%E7%8F%BE%E8%AD%A6%E5%91%8A%E3%80%8Cwarning-lf-will-be-replaced-by-crlf%E3%80%8D/)
