---
title: Navigation固定上方，往下瀏覽頁面時縮小效果
date: 2017-06-30 17:57:58
tags:
- css
- js
---
此篇講述什麼是Web Sticky效果，固定nav及縮小，及紀錄遇到的困難、改良後的JavaScript寫法。
將會使用到CSS、JavaScript。

### 什麼是Sticky？
固定Nav後會隨著捲軸拉下去而縮小
<!--more-->
[請點我預覽DEMO效果](https://hazelwu2.github.io/AjaxMasonryPhoto/)

![stikcy-effect-demo](http://s3-ap-northeast-1.amazonaws.com/hazel-wordpress/wp-content/uploads/2017/06/11232058/sticky-effect-300x120.gif)


### HTML架構：

![nav-html-elmulate](http://s3-ap-northeast-1.amazonaws.com/hazel-wordpress/wp-content/uploads/2017/06/11232033/nav-html-elmulate-300x247.png)

### 流程圖

![sticky-effect-flow-chart](http://s3-ap-northeast-1.amazonaws.com/hazel-wordpress/wp-content/uploads/2017/06/11232046/sticky-effect-flow-chart-300x201.png)

### 錯誤示範
- [CodePen的錯誤示範](https://codepen.io/wualnz/pen/RgQozy)

![sticky-effect-bad-example](http://s3-ap-northeast-1.amazonaws.com/hazel-wordpress/wp-content/uploads/2017/06/11232110/sticky-effect-bad-example-768x452.png)

- 超新手時期的我居然這樣寫XDD，程式碼真的是又臭又長天啊，把JS和CSS全部寫在一起了=_=
- 千萬不要這麼做，把CSS, JS分離，日後才好維護程式碼！

### 改良後Js寫法
將CSS分歸類在JS要新增的CSS CLASS裡，JS只新增CLASS
一目瞭然！
```
    $(function(){
         $(window).scroll(function(){
          if($(window).scrolltop() > 0)
              $(‘#header’).addclass(‘mini’);
          else 
		      $(‘#header’).removeclass(‘mini’);
          });
   });
```