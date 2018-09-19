---
title: 為什麼前端要學Vue.js？
date: 2018-09-12 15:11:18
tags: Vue
---

## Vue.js 解決了什麼問題？

當考慮要不要學習新技術時，要先問自己幾個問題
1. 這個技術解決了什麼樣的問題？
羊毛出在羊身上，這個技術之所以被研發出來，必有其因。
2. 這個技術有沒有同類型的替代品？比較各自的優缺點
3. 我花時間成本去學習這個技術，對我有什麼好處？同樣的時間花去學別的技術，是不是更好？
4. 有什麼實際的問題我可以實用這項技術直接解決？

### 定義目標
以上這些問題是定義目標，除了增強學習動力之外，也是防止浪費時間學一門沒什麼用的技術。
人的時間與精力有限，於是「選擇」變得很重要。
要將有限的時間、精力投其所好。


### 為什麼要使用 Vue.js？
1. 讓資料與 HTML 能夠完整分離
2. 將網頁資料存進「狀態」裡，容易管理。不需監聽才能得知網頁當下值。
根據資料自動複製 HTML、設定HTML屬性
3. 將資料與網頁雙向同步
同步更新資料與網頁的資料
4. 加快開發、程式可讀性增加

### Vue.js 初始化
``` js
new Veu({
  el: '#app',
  data: {
    
  } //資料保存狀態
})
```

資料替換 &#123; &#125;文字 與 HTML
v-html
指定資料時可進行額外計算，使用Js表達式
``` js
data: {
  text: 'Hello',
  htmlText: '<p>Hello HTML</p>'
}
```

v-bind:href 可簡寫成 :ahref
<a v-bind:href="link"></a>
<a :href="link"></a>

v-if：條件不符合時不產生 HTML
v-show：條件不符合時以 CSS 隱藏。（display: none）
v-else：其餘狀況，當物件存在時才顯示。

### v-model 雙向綁定資料
當使用者輸入變動時，會同步反應資料。

指定 v-model 資料型別
確保同步時，使用者變動反應的資料是數字或是布林值

可應用：打勾時變更為true，撲克牌便會翻開

例如：type為number或checkbox，來更動資料是否為布林值

```
input(type='number, v-model='綁定資料')
input(type='checkbox', v-model='綁定資料')
```

### 計算屬性 coumputed
- computed 會建立「虛擬屬性」
- 不實際建立在資料data內，但會建立虛擬的data
- 雙向計算屬性：利用到 getter、setter
取得值或是設定值可以觸發額外動作
  - getter 取得ssetter的實際值
  - setter 設定值
- 常應用在 總價、過濾後的清單或是動態 CSS

例如：購物車的組合價格（商品價格+運費）、組合名字
``` pug
h3 {{ price }}
```
``` js
var vm = new Vue({
  el: 'h3',
  data: {
    fullPrice: 3000,
    discount: 0.85,
    shoppingFee: 60
  },
  computed: {
    price(){
      let result = this.fullPrice * this.discount
      return result + this.shoppingFee // 2610$
    }
  }
})
```
利用computed建立出price的虛擬屬性，來組合價格
#### getter 與 setter
雙向綁定，指定getter與setter

``` pug
input(v-model='fullName')
```

``` js
data: {
  firstName: '',
  lastName: ''
},
computed: {
  fullname: {
    get(){
      return this.firstName + ',' + this.lastName
    },
    set(value){
      this.firstName = value.split(',')[0]
      this.lastName = value.split(',')[1]
    }
  }
}
```
get()會取得使用者輸入的firstName與lastName
set()則會開始處理 get得到的值

### 監看變化 watch
- 變數發生變化時偵測觸發
- 傳入引數：監測出改變前、改變後的值
- 常應用在 表單使用者輸入、改變資料或是登入

