---
title: Vscode eslint 與 prettier 衝突
date: 2021-11-12 15:21:46
tags:
category:
---

解決 Eslint 與 Prettier 之間的衝突

使用 Vscode 同時設定了 Prettier、Eslint 存檔時自動格式化，導致會出現衝突，如下圖
![Vscode Prettier Eslint 衝突](../../../images/20211112/vscodeAndEslintAndPrettier.gif)
可以看見點擊 cmd+s 存檔後，格式化成功了又會馬上變回別種不合格的格式，這個問題困擾了我很久，在此紀錄


## 衝突原因
衝突的原因不外乎是專使用 Prettier, Eslint，而且也同時開啟自動格式化、自動 fix 程式碼的功能，就是下面這兩行，罪魁禍首！

// .vscode/setting.json
``` json
{
  // 存檔後，prettier 自動格式化
  "eslint.enable": true,
  // 存檔後，啟用 eslint --fix 自動格式化程式碼
  "editor.codeActionsOnSave": {
    "source.fixAll.stylelint": true,
    "source.fixAll": true
  }
}
```
不過我設定寫的很分散，一個 prettier 設定在 .vscode/setting.json，另一個 eslint 格式化設定在 vscode/setting.json 自身的設定檔內
如果你的專案有和其他人一起合作，建議直接寫在 .vscode/setting.json 資料夾內，優先順序會先讀取專案內的 .vscode/setting.json，再來才是讀取 vscode 本身的偏好設定

## 解決方式
把 prettier 的自動格式化關閉即可
``` json
{
  // eslint.enable 改為 false
  "eslint.enable": false,

  // 存檔後，啟用 eslint --fix 自動格式化程式碼
  "editor.codeActionsOnSave": {
    "source.fixAll.stylelint": true,
    "source.fixAll": true
  }
}
```

## Reference
- [解决Eslint 和 Prettier 之间的冲突](https://juejin.cn/post/7012160233061482532)
- [詳解VSCode 格式化不符合预期的问题](https://zhuanlan.zhihu.com/p/103492877)