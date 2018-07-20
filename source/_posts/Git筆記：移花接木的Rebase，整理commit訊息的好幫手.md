---
title: Git筆記：移花接木的Rebase，整理commit訊息的好幫手
date: 2018-03-04 04:42:10
tags:
- Git
---
<a href="https://s3-ap-northeast-1.amazonaws.com/hazel-wordpress/wp-content/uploads/2018/03/04182817/rEBASE.png"><img class="size-full wp-image-334 aligncenter" src="https://s3-ap-northeast-1.amazonaws.com/hazel-wordpress/wp-content/uploads/2018/03/04182817/rEBASE.png" alt="" width="560" height="397" /></a><a href="https://s3-ap-northeast-1.amazonaws.com/hazel-wordpress/wp-content/uploads/2018/03/04193721/rEBASE-1.png">
</a>Git 有個神奇的功能叫：Rebase，中文翻譯叫「變動基底」，簡單來說就是移花接木，把某一個樹枝（Branch）接到Master的主幹上等等功能，指定的節點可以重新 commit，花了一點時間做功課，把自己的理解的寫下來


# 為什麼要用 Rebase ？變動基底的話不就很危險嗎

Rebase 的好處 &gt; 壞處，接著養成好習慣，在每次做Rebase之前，開一個backup的branch，以防改爛時還可以回溯，這樣就什麼都不怕啦！

## Rebase能做什麼？
  - 可以修改 commit 訊息 （Reword）
  - 可以修改 commit 內容（Edit），甚至可以拆成兩個 commit 紀錄、合併commit
  - 重新 commit ( <code>Pick</code>)
  - 更換 commit 順序（<strong>做這個要很小心，可能會把你的專案搞爛</strong>）
  - 修改 commit 順序 (<code>Edit</code>)
  - 拆解 commit (把多個commit紀錄變成一個commit)
  - 壓縮 commit，合併 commit 訊息 (<code>Squash</code>)
  - 刪除 commit (<code>Skip</code>)
即使列出來還是不懂Rebase的好處嗎？在此示範，我常常會有種鬼打牆的狀況.....
### 亂糟糟的Commit紀錄
![](https://s3-ap-northeast-1.amazonaws.com/hazel-wordpress/wp-content/uploads/2018/03/04183630/Screen-Shot-2018-03-04-at-6.34.30-PM.png)

你看到的git log有好幾處幾乎是有重複的commit紀錄，這時就有必要性整理commit紀錄了！

### 用rebase來整理commit紀錄
由於我是在develop的branch開發的，所以整理commit message自然也要在develop上做
首先先git log 查看你想要塗改的commit紀錄，最好是在前面好幾個紀錄前
![](https://s3-ap-northeast-1.amazonaws.com/hazel-wordpress/wp-content/uploads/2018/03/04183958/Screen-Shot-2018-03-04-at-6.38.01-PM-1024x478.png)

如果說要重新commit的話就是選你的要的那一行commit，預設都是pick
`輸入git rebase -i 968cada`

![](https://s3-ap-northeast-1.amazonaws.com/hazel-wordpress/wp-content/uploads/2018/03/04184958/Screen-Shot-2018-03-04-at-6.48.27-PM-1024x670.png)

那我把第二行的commit 調整首頁區塊 `pick` 修改成 `squash`
`squahs`代表將這個commit紀錄，合併到上一個commit裡面

![](https://s3-ap-northeast-1.amazonaws.com/hazel-wordpress/wp-content/uploads/2018/03/04185313/Screen-Shot-2018-03-04-at-6.49.21-PM-1024x743.png)



接著會跳出視窗：請你修改commit message <
![](https://s3-ap-northeast-1.amazonaws.com/hazel-wordpress/wp-content/uploads/2018/03/04185325/Screen-Shot-2018-03-04-at-6.50.13-PM-1024x668.png)


我不想要留下那個commit訊息，我要直接合併，因此我將第二個commit message那一行全部刪除

![](https://s3-ap-northeast-1.amazonaws.com/hazel-wordpress/wp-content/uploads/2018/03/04185343/Screen-Shot-2018-03-04-at-6.50.35-PM-1024x403.png)

### 如何在Vim編輯器下存檔、離開？
> 下圖為`Vim`編輯器下，如果你還不太會用Vim編輯器，只要記得下列兩個超實用的指令就可走遍半個江湖
> 如何修改：按鍵盤的`i`即會出現游標，便可以開始修改。修改完成後按下`ESC`鍵，並輸出`:wq`即可存檔
> 不想存檔？想要直接離開，一樣先按下`ESC`並輸入`:q`即可不存檔離開

![](https://s3-ap-northeast-1.amazonaws.com/hazel-wordpress/wp-content/uploads/2018/03/04185813/Screen-Shot-2018-03-04-at-6.56.36-PM-1024x251.png)


接著就成功的合併commit紀錄啦！

如果覺得還是醜醜的可以繼續rebase整理完再合併到master上

接著就是切換到`master` =&gt; `git checout master` =&gt; 合併 `git merge develop`

&nbsp;

如果選擇其他的像reword、edit的選項（修改commit訊息、或是一個commit拆成多個commit）
他會自動跳到你當時儲存的節點當成是HEAD
接著再`git add 你想要分的檔案` `git commit -m` 就可以拆分commit紀錄了

&nbsp;

整理完develop的commit紀錄後，再`git merge master`就可以囉

別忘了在rebase前，記得開一個backup的branch，以防萬一啊！

### 練習試試看，推薦Learn Git Branching，
我非常推薦一個非常棒的Github教學網站：[Learn Git Branching](https://learngitbranching.js.org/)
要學Git往往最困難的是要準備此情境的檔案，因為很懶（誤）...
但在這個`Learn Git Branching`直接幫你準備好，甚至很變態的在網站上輸入git指令就可以馬上看到結果。
![Learn Git Branching](https://i.imgur.com/tK5aKJG.png)
這邊還可以學到reset, revert, rebase, cherry-pick
![Learn Git Branching](https://i.imgur.com/i1PAyAw.png)
根據你想要學的單元，他會給你一個目標進而完成關卡。
![Learn Git Branching](https://i.imgur.com/QFENxyW.png)
他的介面是這樣，動手在左邊視窗輸入指令，右邊的圖便會照著你的指令移動

> 看再多的教學，都只是用眼睛看而已，一定要動手實作看看，才真的可以吸收進去，快點點進去吧XD