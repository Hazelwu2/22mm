---
title: Git 修改又臭又長的指令，加快開發速度
date: 2018-02-09 05:16:04
tags:
- Git
---
![](https://s3-ap-northeast-1.amazonaws.com/hazel-wordpress/wp-content/uploads/2018/02/09000352/Screen-Shot-2018-02-08-at-11.47.49-PM.png)

### 有辦法縮短Github指令嗎？
`git status`, `git commit -m'`,`git checkout develop/layout`,`git pull origin master`..是不是常常覺得輸入這些指令很繁瑣又長，有甚麼辦法可以縮短這些指令呢？
#### 答案是有的！

### 將Git常用的指令，縮短成好記又好用的縮寫吧！
這四個指令比較好記，也較常用到，實用度很高！！
1. MAC 環境下打開 iTerm
2. 輸入以下指令
```
git config --global alias.co checkout
git config --global alias.ci commit
git config --global alias.st status
git config --global alias.br branch
```
- co = checkout
- ci = commit
- st = status
- br = branch

```
git co develop // 等同於 git checkout develop
git ci -m 'Initalize commit' // 等同於 git commit -m 'Initalize commit'
git st // 等同於 git status
git br -b develop // 等同於 建立develop分支並切換過去
```
#### 其他參考指令
`vim ~/.gitconfig` 找到 `alias` 列表後可以根據自己需求輸入，如果想要客製化更改也可以的哦

1. `alias gthis='git rev-parse --abbrev-ref HEAD'`
2. `alias gpushthis='git push origin gthis'`
3. `alias gpullthis='git pull origin gthis'`
4. `alias gup='git remote update'`
5. `alias gpl='git pull origin'`

### 客製化 Git 輸出顏色
為了好好愛護眼睛，來客製化 Git 輸出的顏色吧!

先開啟git color ui顏色顯示指令。
```
git config --global color.ui true
```

開啟`git color.ui`指令後，Git 會按照你的需要，自動為大部分的輸出加上顏色。
當然也能客製化的更改，可以明確地規定哪些需要指令需要塗上顏色、要設定什麼顏色
![](https://s3-ap-northeast-1.amazonaws.com/hazel-wordpress/wp-content/uploads/2018/02/09000352/Screen-Shot-2018-02-08-at-11.47.49-PM.png)

- 用 Vim 編輯器直接進入 gitconfig 來編輯
`vim ~/.gitconfig`
- 找到 [alias]列表就可以開始進行客製化啦！
- 我使用別人提供的 Pretty git branch 加上 [alias]上

``` bash
lg = log --graph --abbrev-commit --decorate --format=format:'%C(yellow)%h%C(reset)%C(auto)%d%C(reset) %C(normal)%s%C(reset) %C(dim white)%an%C(reset) %C(dim blue)(%ar)%C (reset)' --all
```
以後只要輸入 git lg就會有一個小型的分支圖+一行的commit訊息啦，非常方便！

#### [Gitgraph.js](http://gitgraphjs.com/)
也是一個js寫的library在瀏覽器顯示分支圖的樣式，有興趣者可以研究看看

#### [bash-completion 自動補齊 Plugin](https://github.com/scop/bash-completion)
- MAC 環境 Bash安裝 bash-completion 
- 工程師實在要記得太多數不清的指令了，當忘記指令的時候，多按幾下TAB便會幫你自動提示，超強大神器！

Mac 使用 Brew 安裝 bash-completion，在 commend上輸入以下指令即可安裝。
`brew install git bash-completion`

> 若還沒有安裝 `Brew` 請往前走左轉到 [Brew官方網站安裝](https://docs.brew.sh/Installation)，啊！對了只限定於 Mac 哦。

### Reference 參考資料
1. [Bash 自動補齊 on Mac OSX 10.11.1](http://note.drx.tw/2015/11/bash-completion-on-mac-osx.html)
2. [前端資源系列- Git-常用命令&快捷命令&小工作流](https://segmentfault.com/a/1190000005945614)
3. [Git-客製化-Git-設定](https://git-scm.com/book/zh-tw/v1/Git-%E5%AE%A2%E8%A3%BD%E5%8C%96-Git-%E8%A8%AD%E5%AE%9A)
4. [Pretty git branch graphs](https://stackoverflow.com/questions/1057564/pretty-git-branch-graphs)