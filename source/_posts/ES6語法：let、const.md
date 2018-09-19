---
title: ES6語法：let、const
date: 2018-09-04 02:06:54
tags: ES6
---
## let 與 const 誕生的目的
不會汙染全域變數，好方便後續維護、Debug

1. let, const 用來宣告區塊 {} 裡的變數
2. for迴圈宣告 `var`改為 `let`
3. const 唯讀變數，無法去做修改。

let存在於大括號裡面，在大括號外面便會死翹翹。

## 被全域變數污染的例子
``` pug
ul.list
  li 1
  li 2
  li 3
```
``` js
  const list = document.querySelectorAll('.list li').length;
  for ( var i = 0; i < list; i++ ) {
    document.querySelectorAll('.list li')[i].addEventListener('click', function(){
      alert(i + 1)
    })
  }
```
for迴圈宣告 `var i = 0`，導致全域變數被污染，不管點擊哪個 li ， alert 出來的訊息皆為 4。在 Console輸入 i 便會出現3
但當for迴圈宣告是`let i = 0`，將var改為let後，預期點擊 第一個li時 便會出現alert 1、預期點擊第二個 li 時 便會出現alert 2。

## 如何知道有沒有污染全域變數？
在Browser Console 輸入 window.變數名稱，一試便知有沒有被污染。
輸入window.變數名稱，如果有出現回應，代表此變數在全域中是存活的，也就是被污染了。

## const 例外情況
當 const 為 物件或陣列時，便可以修改。
當 const被宣告為物件、常數時，記得要加上`freeze`方法來凍結。

``` js
const obj = {
  url: 'http://wualnz.com'
}
obj.url = 'http://helloWorld.com'
```
obj.url重新定義為`http://helloWorld.com`時，成功被修改了。

當使用陣列、或物件時，有沒有什麼辦法不被修改？
有，使用 freeze（凍結）
``` js
const obj = {
  url: 'http://wualnz.com'
}
Object.freeze(obj)
obj.url = 'http://helloWorld.com'
```
當加上`Object.freeze(obj)`後，便無法再更改obj.url了，於是後面定義的helloWorld便會報錯。

## 使用 let、const 時機
當宣告變數時，你必須先評估這個變數未來會不會常被更改？
不希望這個變數被其他開發者修改、亂搞時，宣告 `const`。
未來還是會修改，使用 `let` 來宣告。

### let 與 const 特性
1. `let`, `const` 不會向上提升（Hoisting）
let 與 const 直到讀取那一行宣告，才會被讀取到。
宣告 var 時， Javascript 會將它提升（Hoisting），但當使用 let, const是不會被提升的。
let、const 不會提升(Hoisting)，但 var 會被提升。

以下是用 var變數後會被向上提升的範例
``` js
console.log(a); // undefined 
var a = 3
console.log(a); // 3
```

用let或const宣告，會直接報出錯：a is not defined

``` js
  console.log(a); // Uncaught ReferenceError: a is not defined
  let a = 3;
  console.log(a);
```

2. 在同個區塊上（大括號內）不能重複命名
3. let, const 不會變成全域變數
當在全域宣告 let, const的變數時，當你使用 window查找，會發現用 let, const宣告過的變數回報：undefined。

``` js
var a = 1;
window.a // 1

const a = 1;
window.c // undefined

let b = 1;
window.b // undefined
```
即使你在全域環境下宣告let或const，使用window查找皆會出現undefined。