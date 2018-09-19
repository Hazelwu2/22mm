---
title: Canvas：前端特效動畫的基礎
date: 2018-09-13 11:21:09
tags: 
  - canvas
---
## Canvas 基礎特性
![像素圖示範](http://bpic.588ku.com/element_pic/12/03/20/1656e3909271e51.jpg)
1. 可以自由繪製的元件區域
2. 可以控制每個像素的顏色與繪製
3. 有很高的操控度，就像遙控器
4. 可以把他當成一張動態隨時可更動的圖片

## 掌握 Canvas
1. 繪製圖型
2. 向量概念
3. 三角函數數學
4. 物件導向開發（拆成函件或物件變成小零件，來操作。方便除錯與維護）

## 點線面構成圖形

1. 用點和線連成面
2. 根據路徑填色或畫線

## Canvas 的座標系方向
1. 原點預設在左上角(0,0)
也因為原點在左上角，因此 Y軸 增加是往下跑的
2 . 角度方向是逆時鐘的

## 如何開始畫 Canvas
1. 初始化 Init Canvas Element
``` js
var canvas = document.getElementById('canvas')
var cvs = canvas.getContext('2d')
```
2. Setting Canvas 畫布尺寸
``` js
canvas.width = window.innerWidth
canvas.height = window.innerHeight
```
3. 開始畫圖，構成三角形
  - 我要開始新的路徑 
  `cvs.beginPath()`
  - 點移到多少
  `cvs.moveTo(50, 50)`
  - 線填滿
  `cvs.lineTo(100, 100)`
  `cvs.lineTo(250, 20)` 
  - 關閉路徑
  `cvs.closePath()`
  - 填滿顏色
  `cvs.fillStyle='black'`
  `cvs.fill()`

### Canvas 矩形繪圖函數
- 填滿矩形 
`fillRect(x,y,w,h)`
- 繪製線框矩形（正方形但沒有填滿、只有邊框）
`strokeRect(x,y,w,h)`
- 清除矩形範圍
清除指定矩形區域內的內容，使其變為全透明。
`clearRect(x,y,w,h)`

> (x,y,w,h)各代表什麼意思？
> x,y 表示從原點（左上角）為出發的座標位置
> width, height表示矩形的寬與高

### 路徑繪圖
路徑開始與封閉：beginPath / closePath
移動與畫線：moveTo / lineTo / arc
指定填色或線條顏色： fillStyle / lineStyle
把路徑填色或描出來 stroke / fill

### 轉移
- Translate(x,y)
- Rotate(deg)
以當下為中心做旋轉
- scale(x,y)

### 狀態的保存與還原
將所有繪製的畫布放在 ctx.save()和ctx.restore()，能預防被污染。
- ctx.save()
儲存當下的座標
- ctx.restore()
還原上一個儲存的狀態

還原的原則：先進後出(stack)，像是小畫家回上一步的狀態

範例：
利用儲存狀態，將函數產生的座標變化限制在函數內
``` js
function drawSomething(x, y, angle) {
  ctx.save()
  ctx.translate(x, y)
  ctx.rotate(deg)
  ctx.restore()
}
```

### 矩陣
利用 setTransform 來設定矩陣
```
ctx.setTransform(A, B, C, D, E, F)
```
重設狀態：
ctx.setTransform(1, 0, 0, 1, 0, 0)

## Reference
- [Web APIs Canvas 介紹 | MDN](https://developer.mozilla.org/zh-TW/docs/Web/API/Canvas_API/Tutorial/Drawing_shapes)