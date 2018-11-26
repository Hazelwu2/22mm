---
title: JavaScript ES6 物件 Literal Upgrades：動態命名
date: 2018-11-25 19:12:34
tags:
- ES6
---
### ES6 Object Literal Upgrades
ES6 擴展：物件實體命名，透過`[]`的方式，可以帶入變數，讓程式碼更好維護、管理。
### 運用方式：像是對物件裡的屬性命名時，能夠用像是組字串的方式，讓命名名稱更有彈性。
### 舉例：
像是我建立一個tShirt的物件，裡面有屬性是Color：color色碼

```
const key = 'pocketColor';
const value = '#d6b161';

const tShirt = {
  [key]: value,
}
console.log(tShirt);
```
console.log後的結果，[key]是什麼？
沒錯，就是'pocketColor': '#d6b161'

假設今天的目的是在一個物件裡放A顏色、A相反的顏色，並配對值
和A相反的顏色命名為pocketColorOppsite，並用invertColor函數來取出顏色的相反
我們可能會這樣寫：

``` js
const tShirt = {
  [key]: value,
  pocketColorOpposite: invertColor(value)
}

function invertColor(color) {
    return '#' + ("000000" + (0xFFFFFF ^ parseInt(color.substring(1),16)).toString(16)).slice(-6);
}
```
套用 ES6 擴展物件屬性，我們可以帶入變數的方式
ES6 的寫法可以這樣下：
``` js
const tShirt = {
  [key]: value,
  [`${key}Opposite`]: invertColor(value)
}
```
透過[]方式，屬性名稱也可以使用`${}`的方式放入變數
也會較好維護。

### Reference
- [ES6 Object Literal Upgrades](https://github.com/wesbos/es6-articles/blob/master/33%20-%20Object%20Literal%20Upgrades.md)
- [ECMAScript 6 入門](http://es6.ruanyifeng.com/?search=object+literal&x=0&y=0#docs/string#%E6%A8%A1%E6%9D%BF%E5%AD%97%E7%AC%A6%E4%B8%B2)

