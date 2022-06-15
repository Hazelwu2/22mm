---
title: Vscode code重開機後指令失效
date: 2022-06-15 17:35:45
tags:
category:
---
Vscode 有個功能，Launching from the command line，在終端機移動到專案資料夾，輸入 `code .`，即可開啟 vscode 該專案目錄
![Vscode Shell Command](https://code.visualstudio.com/assets/docs/setup/mac/shell-command.png)

最近遇到一個問題很煩人，每次重開機，終端機輸入 `code .`，會失效，需要重新安裝，找到解決方案，在此寫下這篇文章

## 解決方式
終端機會有常用習慣的 `bash` 或 `zsh` 等，例黑手常用的是 Mac iterm2 使用 `zsh`，可以直接開啟 `zshrc` 或 `zprofile` 手動設定指令

指令來自[Vscode官方文件](https://code.visualstudio.com/docs/setup/mac#_launching-from-the-command-line)

### zsh 是什麼？
大多數人使用 mac 都會用 zsh 而非 bash，因為 oh-my-zsh 這個工具兼容了 bash之外，還擴充了自動補全、背景主題好看的樣式等功能
[zsh官網](https://ohmyz.sh/)

Mac 通常 .bashrc, .zshrc 等檔案皆會放在 `~/` 目錄下，例如 `/.zshrc`,  `~/.bashrc`

### zsh 設定
在終端機輸入以下指令，會將 export PATH 自動加入在 `~/.zprofile` 內
```bash
cat << EOF >> ~/.zprofile
# Add Visual Studio Code (code)
export PATH="\$PATH:/Applications/Visual Studio Code.app/Contents/Resources/app/bin"
EOF
```

### bash 設定
在終端機輸入以下指令，會將 export PATH 自動加入在 `~/.bash_profile` 內
```
cat << EOF >> ~/.bash_profile
# Add Visual Studio Code (code)
export PATH="\$PATH:/Applications/Visual Studio Code.app/Contents/Resources/app/bin"
EOF
```

輸入完後，可以測試看看，重新開機後在終端機輸入 `code .`，設定成功的話便會成功開啟 Vscode 囉 


如果問題還沒有解決，參考別人的案例，也有可能是 Vscode 不是安裝在應用程式(Application)資料夾下，而是安裝在 下載(Download)資料夾，導致無法開啟。

將 Vscode 重新安裝，並移動到 Application，也許可以解決你的問題


## Reference
- [Launching from the command line](https://code.visualstudio.com/docs/setup/mac#_launching-from-the-command-line)
- [VSCode Shell Commands not retaining install status in $PATH after restart ](https://github.com/microsoft/vscode/issues/42051)