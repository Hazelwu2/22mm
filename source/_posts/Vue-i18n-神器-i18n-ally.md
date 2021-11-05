---
title: '[Vue] i18n 神器 i18n-ally'
date: 2021-10-13 18:15:07
tags:
---

VS Code 開發一個套件叫 i18n-ally，常常我們需要對照 i18n 翻譯，要另外再開啟 json 檔非常混亂，安裝此套件後就可以輕鬆對照啦，此外還有提供進度表，可以告訴我們各種語言翻譯的進度，非常方便的神器
![i18n-ally](../../../images/20211013-vue-i18n-ally.png)

1. 點擊 VSCode 套件安裝 i18n-ally
2. 編輯 setting.js
可用 「cmd+,」，再點開右上角的 A4 檔案 Icon，會開啟 setting.json 檔
在最下面加上你的 i18n 路徑。
例如以下的範例，我的 i18n json 檔放在 src/i18n/config 底下，調整成你放的實際目錄
預設顯示語言可以設定，像我的語系有 en, zh, tw，也可以預設顯示為 tw
``` json
{
  // 語系檔路徑
  "i18n-ally.localesPaths": [
    "src/i18n/config"
  ],
  // 默認顯示翻譯文字
  "i18n-ally.displayLanguage": "cn",
}
```
編輯完成！
在左側旁邊的功能列找到 i18n-ally，也可以看到當前檔案及翻譯進度
當前檔案會列出所有 json 第一層的檔案
![i18n-ally](../../../images/20211013-vue-i18n-ally2.png)