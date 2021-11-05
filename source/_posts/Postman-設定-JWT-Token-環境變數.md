---
title: '[Postman] 設定 JWT Token 環境變數'
date: 2021-10-20 00:33:00
tags:
- postman
---
## Postman Test 設定
Login API 選擇 TEST 設定
![Postman Test to setting environment accessToken](../../../images/20211020/postman-test.png)

``` js
pm.environment.set('accessToken', pm.response.json().token)
```
根據你的 API 吐回的 Token 設定到 Postman 環境變數裡
設定好之後，每次打登入，API吐回的 token 會寫進 Postman 環境變數 accessToken 裡。

``` json
{
    "status": 1,
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6IjYxNmVlZTk5YWVlNjdiNDRkZWU5ODIyMCIsImlhdCI6MTYzNDY2MDQ3OCwiZXhwIjoxNjQyNDM2NDc4fQ.tyq6Dd32ROnIuVdCed3xQel6E-jg8YcaCRcoEqSQs44",
    "message": "success!"
}
```
登入 API，因返回 JSON 格式是 data.token，故在測試裡，pm.response.json() 表示返回來的 data，而要取得 token，故寫成 pm.response.json().token。

### Postman Console
如果一直取得不了你要的值，可以用 console.log 印出來，如圖下所示，開啟 Postman Console 視窗，在 Test 寫下 console.log 後重新請求，便會看到 console 控制台出現 pm.response.json()

![Postman ConsoleLog](../../../images/20211020/postman-console.png)

## 新增 Headers Authoriazation
需要登入才可以請求的 API，例如 `/api/v1/tours`，開啟 Postman 這單支 API 切換到 headers
新增 Authorization: Bearer {{accessToken}}，設定好後，當請求 `/api/v1/tours`，便會自動帶入 Postman accessToken 變數囉

![Postman Add Headers Authorization](../../../images/20211020/postman-addHeaders.png)

另外也可以在 Tab Authorization 這裡設定，帶入 {{accessToken}} 即可，兩個地方擇一都會有一樣效果，請求會自動代入環境變數 accessToken

![Postman Authorization](../../../images/20211020/postman-authorization.png)
