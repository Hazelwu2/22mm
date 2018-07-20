---
title: Git 筆記：我想回到過去，到底要怎麼坐時光機？
date: 2018-02-06 05:32:47
tags:
- Git
---
# Git 筆記：我想回到過去，到底要怎麼坐時光機？

### 基本觀念
1. 目前的位置叫做HEAD
2. 沒被 `git add`進入`commit`的檔案`untracked files`，Git會選擇忽略，也不用怕被坐時光機回溯影響，`reset、checkout`對他們的影響力是0。
3. `origin/master`是遠端的resp，`master`是本端的repo

### 我不想玩了不想存檔，我要回到上一次的存檔commit紀錄
懶得看`git log`查代碼，想快速回到上一次的commit紀錄

```
git checkout HEAD^
HEAD^ 等同 HEAD^ 等同 HEAD~
// 都代表一次的意思
```

### 改壞掉了QQ，我想捨棄所有還沒commit的修改
招喚reset!
```
git reset --hard
```

#### 改壞很久才後知後覺，commit存檔之後後悔了，有後悔藥嗎？
1. 退回任意的commit位置
2. 和checkout一樣，可以隨意亂跳

```
git reset --hard HEAD^^
git reset --hard 5efdc4f
git reset --hard master~2
執行結果都一樣，從 HEAD 跳回兩個前 commit
```

開分支寫一寫，然後和master合併

```
git checkout -b feature/fixbug
// 開始寫寫寫寫寫
// 準備合併！
git checkout master
git merge feature/fixbug
```

- 預設的遠端名字習慣都叫 **origin**

```
git remote add origin http://github/.....
```

- 推上遠端的 master / 分支

```
git push origin master
git push origin feature/fixbug
```
- 拉回來 遠端的 master / 分支

```
git pull origin master
git pull origin feature/fixbug
```

### 多人協作開發，Git不打架？解決衝突
#### 先 Clone 克隆再說
1. `git clone http://github...` 
2. 某A 開發寫寫寫寫寫，某B也在寫寫寫
3. 某A `push` 上去了
4. 某B 也準備`push`上去`被拒絕!!!`發生衝突
`pull = fetch & merge`
某B 要先 `git pull`，同步某A版本才行

#### 狀況一
狀況一：git pull 順利成功
1. git pull
2. git push origin master

#### 狀況二
狀況二：git pull 自動 merge失敗：`Merge conflict in xxx.html`
1. 打開`xxx.html`，編輯你要留的部分
2. `git add xxx.html`，加入索引
3. `git commit -m 'merge ...'`

### 臨時狀況
常遇到臨時狀況，需要放下手上的東西，支援其他人
- 狀況一：寫到一半，臨時被叫去修BUG，要切換`branch`怎麼辦？
- 狀況二： 想切換 `branch`、`坐時光機回到commit歷史紀錄`，但手上才寫到一半，我也不想commit

1. `git add .`
2. `git commit -m 'Not finish yet ORZ "'`

可是，難道只能先`commit`嗎？可是還沒完整寫完啊，我不想`commit`，`git log` 會變很亂！

#### 雜事放一旁，先救火再說！
`git stash`就像是把手邊還沒整理好的東西，先全部放在一個箱子裡，等有空時再去箱子拿回來繼續處理。
1. `git stash save "RWDmobile"`
把手邊的資料全部放進RWDmobile的箱子裡
2. `git stash pop "名字"`
終於忙完了，回來繼續整理，取回RWDmobile的箱子，並把箱子丟掉。
3. `git stash drop`
把箱子和裡面的資料，都一起丟掉。
4. `git stash apply 名字`
和pop一樣，取回箱子的資料，但不把箱子丟掉。
5. `git stash list`
查詢我有幾個暫存的箱子


#### 我commit了，但我想修改commit的訊息
- 只能修改 HEAD 上的 commit(目前git 最後一個commit的位置)
- ammend是修改的意思。
``` 
git commit --amend
```

### Git 竄改 Commit 紀錄
想竄改紀錄？請找專家認證的「`Git rebase -i`」
#### 使用情境
1. 我想修改 commit 的順序
2. 我想整理 branch 上的commit
3. 我想先把某個 branch 上的 commit 先 merge 回 master
4. 我想合併 commit，避免太過瑣碎的 commit
### Git rebase 能做的事
- 重新 commit ( `Pick`)
- 更換 commit 順序
- 修改 commit 順序 (`Edit`)
- 拆解 commit (把多個commit紀錄變成一個commit)
- 壓縮 commit，合併 commit 訊息 (`Squash`)
- 刪除 commit (`Skip`)

`squash`
把現在的 commit 和 上一個 commit 合併
`fixup` 
也是合併 上一個 commit ，但不會保存 commit 的訊息

### 把多個 commit 紀錄合併成一個
- 方法一
1. `git stash`
暫存把目前修改的資料通通先封裝進 stash 箱子
2. `git reset 2q542f`
坐時光機回到過去，回到 2q542f 版本
3. `git stash pop`
把箱子裡的資料重新拿出來，所以現在是 2q542f 版本 +上剛剛從 stash箱子拿出來的資料
4. 重新 commit ! 
`git add` 
`git commit -m 'Message'`

- 方法二
1. `git rebase -i de534f`
2. 用 `squash` merge 3 commits into 1 comit


### Rebase完後悔
已經 rebase 竄改完紀錄，但一時眼花看錯，需要還原，對我就是後悔了，該怎麼辦？
#### git reflog
輸入`git reflog`後會出現像`git log --oneline` 頁面
`git reset --hard HEAD@{3}`
根據上面的流水編號或bceefbc值，選你想要回復的位置

#### 將追蹤的某個檔案回復成上一次 commit 狀態
`git checkout -- user.html.erb`
#### 所有已加入`git add`track中，修改過的檔案，復原成最近一次commit 狀態
`git checkout .`
#### 從某個 commit 切回原本 branch
`git checkout -`
#### 將 某branch 裡的 某個 commit 的變動 merge 到另一條 branch
EX: develop 想 merge feature的 #bceefbc commit進來
`git cherry-pick`


### Git 協作流程
開發新功能或跟別人協作git時
1. 新建一個獨立的分支
2. 提交分支的 commit
3. 要時常跟上遠端的腳步，與遠端Responsitory 同步
`commit前，pull！push前，也pull！`
#### pull！pull！pull！pull！(壞掉了嗎？)
`git pull --rebase`
```
1. git pull
2. git commit
3. git pull
4. git push
```
4. 合併 commit
```
git fetch origin
git rebase origin/master
或是
git pull origin master --rebase
```


### Merge 後的訊息好多餘，有辦法簡略嗎？
branch 要 merge origin/master，都會有個 merge commit 'Merge branch 'master'' ，太多餘了！ 這種多餘節點很阿雜，能不能不看到？

輸入`git pull --rebase` 就會使 merge 每次的 commit 不見
> 輸入的前提：使用--rebase是在沒有 conflict 的情況下才使用

真不湊巧，使用 --rebase 結果 conflict 了
`git rebase --abort` 回復 pull 前的狀態
`git merge origin master` 不過就會有多餘的merge message出現

`git pull --rebase`
它等於以下兩步
git fetch
git merge origin 

#### `git pull --rebase` 是什麼意思？
1. 把 commit 但還沒 push 上去的東西暫存丟到旁邊去
2. 現在的 commit 非常乾淨，Git表示：沒有東西要push上去
3. 從遠端 repo 把新的東西 pull 下來，跟上進度。此時你本地端是沒有任何要 push 的 commit，所以也不會有因為有東西還沒 push 所以無法 pull 的情況
4. 再把那些待 push 的 commit 更動過的檔案 回復回來

有 commit 還沒 push -> 想要 pull 同步 遠端repo
變成 輸入 `git pull --rebase` 指令後
把 還沒 push 的 commit 存起來，本地端沒東西 push  -> 先 pull同步 -> 接著變成同步遠端了，目前還有 commit 還沒 push


### Reference | 參考資料
- [Git Merge & Fast-forward](https://qiita.com/vc7/items/6e06b0306c8a64a23263)
- [Git 常用指令](http://blog.jex.tw/blog/2013/07/21/git/)
- [使用 git rebase 避免無謂的 merge](https://ihower.tw/blog/archives/3843)
- [史上最簡單易懂的Git教學：Git 實務圖解](https://www.slideshare.net/pokaichang72/git-42427674)