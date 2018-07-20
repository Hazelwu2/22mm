---
title: VS Code 使用erb類型檔案時，Emmet快捷打html標籤跳不出來
date: 2018-01-05 05:25:17
tags:
- VS Code
- Emmet
- Rails 5
---
![](http://codelikeapoem.com/wp-content/uploads/2017/11/Visual-Studio-Code-For-Windows.jpg)
### 問題：使用VS code erb 無法自動跳出Emmet
從Sublime跳到使用VS Code，已很習慣用Emmet來跳html標籤，但在用RoR(Ruby on Rail)時，像是index.html.erb這類的檔案，打`.container` TAB鍵應該會自動跳出`<div class="container"></div>`，卻毫無反應。

#### 加入一行設定輕鬆解決
在VS Code Setting裡加上這一行就可以搞定
```
"emmet.includeLanguages": {"erb": "html"}
```

### Reference | 參考資料
- [Emmet isn't recognised in erb](https://github.com/rubyide/vscode-ruby/issues/197)