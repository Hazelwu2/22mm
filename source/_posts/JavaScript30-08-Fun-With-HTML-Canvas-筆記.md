---
title: JavaScript30/08 | Fun With HTML Canvas 筆記
date: 2018-10-30 23:42:20
tags: js30
---
JavaScript30/08 | Fun With HTML Canvas 筆記

## Demo 效果
![demo](https://github.com/Hazelwu2/JavaScript30/blob/master/day8-fun-with-html5-canvas/demo.gif?raw=true)
## 完成 Day8 需要用到的屬性
- Canvas
  基本屬性
  - getContext()
  - lineCap
  - lineJoin
  - beginPath()
  調整色彩、寬度
  - lineWidth
  - fillStyle
  - strokeStyle
  繪製屬性
  - beginPath()
  - lineTo(x, y)
  - stroke()
- Event：用各種事件來控制 isDrawing 開關
  - mousedown
  - mouseup
  - mouseout
  - mousemove
<br>

#### 步驟
1. 將 Canvas 元素存入變數，設定 Canvas 寬與高
2. 利用 getContext('2d')，來獲取值 ctx
3. 設定 ctx 的基本屬性
  - 線條顏色（hsl）
  - 線條寬度（lineWidth)
  - 線條末端的形狀
4. 繪畫效果
  - 設定繪圖中的開關（isDrawing）
  - 監聽滑鼠事件，根據不同事件，將開關設定為 true 或 false
5. 動態設定彩虹漸變（利用 hsl 的 h 值，以 h++更改顏色）
6. 動態設定：當繪畫時，線條由細到粗、由粗到細

#### Canvas 起手式
以下為 Canvas 的基本用法
將 Canvas 元素存入變數 canvas內，並設定 Canvas 寬與高
``` html
<canvas id='draw' width='800' height='800'></canvas>
```

設定 Canvas 寬高

``` js
const canvas = document.querySelector('#draw');
const ctx = canvas.getContext('2d');
canvas.width = window.innerWidth;
canvas.height = window.innerHeight;
```
接著，設定用來 ctx 吧
- lineCap：筆觸的形狀，可以設為 round、butt、square
- lineJoin：線條比較方式
- lineWidth：線條寬度
- strokeStyle：線條顏色
- fillStyle：填充顏色
<br>
用 ctx 繪製 Canvas
- beginPath()：開始畫新的路徑 
- stroke()：繪製線條
- moveTo(x,y)：以x軸、y軸為座標位置，操作起點
- lineTo(x,y)：同樣以x,y軸為坐標，從moveTo到lineTo會畫出一條線

#### 設定繪圖開關
理想結果是像小畫家一樣，當點滑鼠左鍵便開始畫線條，直到放開左鍵，所以我們要做一個叫isDrawing的開關，用來控制 繪圖中 / 結束繪圖的狀態。
<br>
- 當點擊左鍵，表示正在畫畫，isDrawing => true，isDrawing開啟。
- 當放開左鍵，表示結束畫畫，isDrawing => flase，isDrawing關閉。

``` js
let isDrawing = false;
canvas.addEventListener('mousedown', isDrawing = ture);
canvas.addEventListener('mousemove', draw);
canvas.addEventListener('mouseup', isDrawing = false);
canvas.addEventListener('mouseout', isDrawing = false);
```
當點擊滑鼠左鍵開始畫圖，便是mousedown，一但點擊左鍵，將isDrawing開關變成 ture。
當滑鼠開始移動時，便執行draw function，裡面將會做一些 Canvas的繪製屬性動作，像是beginTo, lineTo, moveTo
而滑鼠放掉（mouseup）、滑鼠移出canvas的範圍時（mouseout），便將isDrawing變更為 false，代表使用者已不在繪畫中狀態。
<br>
### 線條銜接問題
若第一次畫完後，想再畫第二次，會發現銜接的線條是從上一次開始。但我們希望理想結果是：不要銜接上一次畫的線條
<br>
為了解決這樣的狀況，我們需要更新lastX, lastY，意味著最後滑鼠的x, y軸的座標位置。
#### 要更新座標位置的有兩處
- 當正在繪圖中（isDrawing = ture）時，draw function需更新座標。
- 監聽點擊滑鼠左鍵時，要開始繪圖時，需更新最後位置狀態
如下列程式碼所展現，e.offsetX, e.offsetY 則為滑鼠在網頁上的座標位置，宣告變數lastX, lastY並指定給他們，即可解決線條銜接問題，每一次點擊滑鼠左鍵都是新的開始囉！

``` js
const draw = () => {
  ctx.beginPath();
  ctx.moveTo(lastX, lastY);
  ctx.lineTo(e.offsetX, e.offsetY);
  ctx.stroke();
  [lastX, lastY] = [e.offsetX, e.offsetY];
}

canvas.addEventListener('mousedown', (e) => {
  isDrawing = ture;
  [lastX, lastY] = [e.offsetX, e.offsetY];
})
```
### 動態變動線條顏色、粗細
線條顏色我們以hsl(0, 100%, 50%)來表示，hsl(0, 100%, 50%)，這三組數字個別代表的是色相 Hue、飽和度 Saturation、亮度 Lightness，我們將會把色相替代為動態變數，以變更顏色。
<br>
對 hsl 想要最快速了解，可以前往[Mother-Effing](http://mothereffinghsl.com/)，直接展示給你看 hsl的數值及呈現顏色。

#### 動態設定筆觸顏色
今天想要做到的理想效果是：隨著滑鼠移動畫線條時，顏色也會跟著改變，很簡單，只要將其中一個數值（0, 100%, 50%）改為變數，並將變數每次畫完時都遞增。
<br>
粗細也是囉，Canvas 用 lineWidth來控制線條的粗細程度，將固定數值改為動態變數取代，並將動態變數遞增即可。

要注意的是 hsl值的色相（hue）值最大為 360，所以也需要另外設條件是 當 hue > 360，hue = 0 歸零開始。
<br>
#### 動態設定筆觸粗細
至於控制筆觸大小，設定判斷式：當超過某值時便將變數遞減，當小於某值時便把變數遞增即可達成囉。

``` js
let hue = 0;
let controlLineWidth = 50;

ctx.strokeStyle = `hsl(${hue}, 44%, 50%)`;
ctx.lineWidth = controlLineWidth;
ctx.beginPath(); // 開新的路徑
ctx.moveTo(lastX, lastY);
ctx.lineTo(e.offsetX, e.offsetY);
ctx.stroke(); // 畫線

hue++; // 畫線結束後，將動態變數hue遞增
hue > 360 ? hue = 0 : ''; 

// 控制筆觸粗細大小，設定判斷式
let direction = ture;
if (ctx.lineWidth > 100 || ctx.lineWidth < 80 ) {
  direction = !diection;
}
direction ? controlLineWidth++ : controlLineWidth--;

```
### Reference
- [Mother-Effing 查詢 HSL](http://mothereffinghsl.com/)
- [08 HTML5 Canvas 实现彩虹画笔绘画板指南](https://github.com/soyaine/JavaScript30/tree/master/08%20-%20Fun%20with%20HTML5%20Canvas)