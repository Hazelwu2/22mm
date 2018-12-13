---
title: FrameWork 7 與 H5 邂逅的雷坑
date: 2018-12-13 13:35:45
tags:
- Vue
- FrameWrok 7
---
環境配置：Vue + FrameWork 7 ，外面包一層Apk，做成App供手機測試
以下將紀錄在實作時遇到的雷坑及如何解決的。

## input 送出按鍵 
```js
<!-- 驗證碼: authenticationCode Start -->
<f7-list-item v-else>
  <f7-input
    type="tel"
    class="input-border"
    placeholder="input verification code"
    :value="authenticationCode"
    label="Phone"
    @keyup.enter="confirmActive"
    @input="authenticationCode = $event.target.value"
    pattern="[0-9\.]*"
    error-message="Verification code only be a number."
    clear-button
    validate
    required
    maxlength="4"
    minlength="4"
  ></f7-input>
</f7-list-item>
<!-- 驗證碼 End -->
</f7-list>
```

遇到一個問題是：手機開啟App後，按下送出鍵會原地Refresh重整頁面，不會真的送出。
於是使用`@keyup.enter.native="confirmActive"`方法，發現仍行不通。

經過萬能的Google搜尋、Debugger後，發現了原則：

`@keyup.enter="confirmActive"` => 手機按下送出後，執行confirmActive方法
`@keyup.enter.native="confirmActive"` => 電腦按下送出後，執行confirmActive方法
