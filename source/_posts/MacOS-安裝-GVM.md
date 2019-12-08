---
title: MacOS 安裝 GVM
tags:
  - Go
  - GVM
date: 2019-12-08 16:52:20
---


我用MacOS作業系統，iterm2、zsh環境，這邊做個紀錄
## 移除 Mac 官方 go 安裝檔
我們要裝GVM，要先移除官方版本的GO 安裝檔，
```
rm /usr/local/go
rm /etc/paths.d/go
```

## 安裝 GVM
zsh
```
zsh < <(curl -s -S -L https://raw.githubusercontent.com/moovweb/gvm/master/binscripts/gvm-installer)
```
bash
```
bash < <(curl -s -S -L https://raw.githubusercontent.com/moovweb/gvm/master/binscripts/gvm-installer)
```

安裝完後重啟終端機，輸入gvm就會有東西了


## Reference
- [gvm on macOS installation cheatsheet](https://andyyou.github.io/2019/04/14/gvm-cheat-sheet/)