---
title: JavaScript 30 Day -  Day1 DrumKits 練習
date: 2017-12-29 06:29:24
tags:
---
JavaScript 30 Day -  Day1 DrumKits

主題：用鍵盤按鍵，模擬打出爵士鼓的音效
![](https://s3-ap-northeast-1.amazonaws.com/hazel-wordpress/wp-content/uploads/2017/12/29025908/Screen-Shot-2017-12-27-at-4.48.58-PM.png)
- [DEMO](https://hazelwu2.github.io/JavaScript30/day1-DrumKit/index.html)
- [Github](https://github.com/Hazelwu2/JavaScript30)


### 流程
#### 1. 利用鍵盤監聽USER鍵盤，知道使用者按了哪個按鍵    
`window.addEventListerer('keydown', playSound);`
#### 2. 設定 function playSound(e)
- 將函數e傳入進去，利用e.keyCode取得對應的audio音檔、div data-key標籤。
- 如果傳入的e.keyCode沒有對應的audio，便中斷function。
- 設定audio音檔無延遲，播放時間為0。
`currentTime = 0;`
- 播放對應的音檔。
- 利用`classList.add('playing')`將div.key加入playing的class。
  `classList.add('class') 等同於 jQuery 的 addClass('class')`。
  
#### 3. 將所有div.key組合成陣列，監聽：當播放完音效後，會停止transform的特效。
    
- 利用`querySelectorAll('.key')`組合起來存在變數keys裡面，querySelectorAll組合的div.key是一個陣列組合。    
- 利用`forEach`的JavaScript語法，將keys陣列取出每個div.key，並設定監聽：若transitionend發生了，執行removeTransition的function。

#### 4. 設定removeTransition 的 function，當發生transform效果時則中斷。    
- 將函數e傳入，倘若e.propertyName != transform時則中斷。
- 同時也移除playing的class，利用`classList.remove('class')`，等同於 jQuery 的 `removeClass('class')`。
	
### HTML架構
```
<div class="keys">
	<div data-key="65" class="key">
	<kbd>A</kbd>
	<span class="sound">Clap</span>
</div>
</div>
```

```
<audio data-key="65" src="sounds/hihat.wav"></audio>
```


   
### JavaScript部分
  
#### 設定對應的audio音檔
利用`document.querySelector`來選擇audio[data-key]，因為keyCode有九種按鈕的設置，將keyCode設為變數，由e傳入找出屬性為.keyCode

#### Audio 音檔無延遲、播放

HTML5的 `audio`新標籤，IE 8 及以下版本都不支援
`<audio src="路徑/clap.mp3"><audio>`

透過JavaScript控制播放音樂
`audio.play()`可以播放音檔
`audio.currentTime()`指定播放的秒數
利用設定currentTime = 0，達到無延遲效果。

#### 用forEach 代替 for 迴圈
第一次使用到JS的forEach，以往都是用for迴圈去跑每個元素，也意外發現forEach比起for迴圈還要整潔、快速！前提是：不打算更改陣列裡的每個元素，就可以使用forEach。
```
var arr = [ data1, data2, data3];
for ( var i = 0; i < arr.length; i++){
	console.log(arr[i]);
}
```
使用forEach是相同的效果，寫法也讓程式碼更加簡短了
```
var arr = [ data1, data2, data3];
arr.forEach((val) => {
	console.log(arr[i]);
})
```

#### Arrow Function 箭頭函式
他是ES6版本的函式簡寫
`() => {}`等同於 `function() {}`，但還是有一些區別。

####  Reference
- [MDN web docs - Arrow Function](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Functions/Arrow_functions)
- [在 JavaScript 棄用 For 迴圈，擁抱 Reduce、ForEach、Filter、Map](https://yami.io/reduce-foreach-filter-map/)
- [箭頭函式](https://eyesofkids.gitbooks.io/javascript-start-from-es6/content/part4/arrow_function.html)