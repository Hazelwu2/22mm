---
title: 前端工程師一定要會的：Sass Mixin神器
date: 2018-01-29 05:29:19
tags:
- Front-end
- Sass
- Mixin
---
Sass Mixin 神器，學會 Mixin 可以讓你在開發上的速度加快許多，快來看看如何使用吧
#### Sass Mixin 主要功能
Sass Mixin 主要功能：省去重複撰寫相同 CSS 的時間
- 降低Pseudo(偽)元素撰寫時的重複性
- 重用群組的 CSS，像是prefix，用`@include`加入群組
- 透過`@include`使用參數

### 示範SASS運用
#### 情境一
> 當你在開發時設計.introduce Class時，結果設計師跟你說：這個背景顏色好醜，我們需要改顏色、整體網站都要更改一下，天啊真是晴天霹靂！這樣改要整個大修耶，別怕別怕！還有Mixin來幫你大幅節省時間！

```
$font-size: 14px
@mixin bg
	background: #000
	font-size: $font-size
.header
	+bg
```
SASS轉譯成CSS後，原始碼會變這樣

```
.header {
	background: #000;
	font-size: 14px;
}
```
是不是快多了呢！！！
寫法：@mixin 命名名稱，需要載入的話就是用「+」來載入mixin名稱

#### 情境二
>  設計師說：背景顏色要隨著網站活動變更，整體網站顏色可能會每隔三四個月都要改一次，你會傻傻的一個一個找header改嗎？當然不！拿起我們的秘密武器Mixin吧
在Mixin裡建立變數

```
$font-size: 13px
$linkcolor: #d6b161
$textcolor: #eee
// 在Mixin裡建立變數，並帶入
@mixin bg($bgcolor)
	background: $bgcolor
	font-size: $font-size
.header
	+bg($linkcolor)
.content
	+bg($textcolor)
```
SASS轉譯成CSS後，原始碼會變這樣

```
.header {
	background: #d6b161;
	font-size: 13px;
}
.content {
	background: white;
	font-size: 13px;
}
```
這樣我們可以直接在帶入變數，隨時更改顏色！超方便！

> - 情境三
>  設計師：「我們再來大改吧！包含RWD也要一起動哦」
>  前端工程師：「天啊！我今晚又無法準時下班啦」

### 關於Responsive Web Design
RWD的@media 動則就是寫上千行，想辦法從min 768px裡找出要改的樣式，是件非常超級麻煩的事，一樣來用Mixin來大量減輕負擔吧

- 傳統寫法

```
@media all and (min-width: 980px) {
	.box1 {
		margin: 50px;
    }
    .box2 {
	    margin: 30px;
	}
}
@media all and (max-width: 768px) {
	.box1 {
		margin: 20px;
    }
    .box2 {
	    margin: 15px;
	}
}
@media all and (min-width: 996px) and (max-width: 1200px) {
	.box1 {
		margin: 100px;
    }
    .box2 {
	    margin: 75px;
	}
}
```
是的，我以前就是這樣寫，當我要找桌機版的.box1去修改時
1. 要先找到`@media min-width:996px and max-width: 1200px`
2. 再從裡面找到class box1

你會找老半天，有可能還會看錯改到平板模式的樣式
整體非常沒有效率也很耗時，你還會很傷眼力。
有個辦法：把三個@media查詢樣式時整合寫在一起，既好寫又好找！

```
.box1 {
	@media all and (min-width: 980px) {
		margin: 50px
	}
	@media all and (max-width: 768px) {
        margin: 20px;
    }
   @media all and (min-width: 996px) and (max-width: 1200px) {
        margin: 100px;
	}
}
```
#### 運用Mixin和變數幫助你減輕眼力負擔
- 先針對media設變數，設定各裝置的寬度width
	`$pc-width: 996px;`
	`$pad-width: 768pz;`
- 新增一位叫「pc-width」的mixin，把@media公式代進去，會變更的值填入變數$pc-width
- 在.box1裡用@include來呼叫Mixin：pc-width
```
$pc-width: 996px;
$pad-width: 768px;
$pcs-width: 995px;

@mixin pc-width(){
	@media all and (min-width: $pc-width) {
		@content;
	}
}
@mixin pad-width(){
	@media all and (min-width: $pad-width) and (max-width: $pcs-width) {
		@content;
	}
}
// 代入後長這樣
.box1 {
	@include pc-width {
		margin: 20px;
	}
	@include pad-width {
		margin: 15px;
	}
}
```
程式碼呢就會長這樣

```
.box1 {
	@media all and (min-width: 996px) {
		margin: 20px;
	}
	@media all and (min-width: 768px) and (max-width: 995px) {
		margin: 15px;
	}
}
```
是不是少掉很多的程式碼！！！

#### 顏色想要深一點？懶得挑色碼？這也沒問題
> 用darken(顏色, 深度)

```
$link: #d6b161;
.box1 {
	background: darken($link, 15%)
}
```

### Reference 
- [使用sass mixin來開發responsive網站](https://blog.hellosanta.com.tw/%E7%B6%B2%E7%AB%99%E8%A8%AD%E8%A8%88/%E5%89%8D%E7%AB%AF/%E4%BD%BF%E7%94%A8sass-mixin%E4%BE%86%E9%96%8B%E7%99%BCresponsive%E7%B6%B2%E7%AB%99)


