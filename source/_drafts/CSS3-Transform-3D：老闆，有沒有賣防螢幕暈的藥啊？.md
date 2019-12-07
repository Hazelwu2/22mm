---
title: CSS3-Transform-3D：老闆，有沒有賣防螢幕暈的藥啊？
date: 2018-09-18 20:18:13
tags:
  - CSS3
---
## CSS3 Transform 3D
最近練習 CSS3 Transform 3D，卡片正面翻轉到背面的特效很酷炫，於是寫下這篇筆記。

這篇 CSS3 主要會應用到的屬性有
- tranform-style:presesrve-3d
- backface-visibility
- transform: rotateY(180deg)
- position

HTML 架構
``` PUG
.father
  .front
  .back
```

示範做一張簡單的 Business Card 翻轉
一開始呈現為正面效果，當點擊或Hover時會翻轉到背面。



``` css
.father
  width: 300px;
  height: 300px;
  backface-visibility: hidden;
  transition: all 1s;

  position: relative;

.father:hover
  transform: rotateY(180deg)

.father > div
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
```


### Reference
- [CSS3 In Arabic | 3D Tranform - Backface Visibility](https://www.youtube.com/watch?v=RtNLlANWSgs)