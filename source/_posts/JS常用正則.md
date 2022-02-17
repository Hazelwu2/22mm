---
title: JS常用正則
date: 2021-12-28 10:43:15
tags:
category:
---
專案常用的 Javascript 正則表達

``` js
// 驗證金額，格式：正整數、小數點不超過兩位
const amountReg = /^(([1-9][0-9]*)|(([0]\.\d{1,2}|[1-9][0-9]*\.\d{1,2})))$/
// 版本號限制，格式v1.0.1，不可輸入到百位數字，例如：v000.000.000, v.00.00.00
const versionReg = /^v[1-9]?\d{1}\.[1-9]?\d{1}\.[1-9]?\d{1}$/
// 加上千分位，例 7654321 -> 7,654,321
let num = num.toString()
return num.replace(/\B(?<!\.\d*)(?=(\d{3})+(?!\d))/g, ',')
// IP驗證
const ipReg = /^((25[0-5]|2[0-4][0-9]|1[0-9][0-9]|[1-9][0-9]|[0-9])\.){3}(25[0-5]|2[0-4][0-9]|1[0-9][0-9]|[1-9][0-9]|[0-9])$/
// 只允許輸入小數點第0-5位
const validateRtp = /^(([1-9]{1}\d*)|0).\d{0,5}$/
// 大小寫英文+數字+下底線
const validEnAndNumberAndSymbolsReg = /^[a-zA-Z0-9_]+$/
// 僅接受大小寫英文、數字、-、底線，不能含有空格
const pattern = /^[A-Za-z0-9-_/S]*$/
return pattern.test(str)

// 檢查網址是否為圖片檔
function checkUrlIsImage(url) {
  return (url.match(/\.(jpeg|jpg|gif|png|svg)$/) != null)
}
// 檢查網址是否為檔案
function checkUrlIsFile(url) {
  return (url.match(/\.(xlsx|xls|doc|txt|text)$/) != null)
}
```

