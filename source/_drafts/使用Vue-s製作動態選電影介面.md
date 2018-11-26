---
title: 使用Vue.js製作動態選電影介面
tags:
- Vue
---

## 電影選擇界面
- 藉由滾輪左右滑動卡片
- 結合 Axios 載入資料 / 呈現網頁
- 使用 TweenMax 輔助動態

### 1. AJAX存取資料
- 使用 axio 動態載入資料
  ``` js
  created(){
    let apiURL = 'https//...'
    axios.get(apiURL).then(res=>{
      this.movies = res.data // [{name: '....}]
    })
  }
  ```

### 2. Vue 生命週期介紹
  -[生命週期](https://vue.js.org/v2/guide/instance.html)
在created週期時建立

### 3. this.$nextTick
  - 跟其他網頁操作函式庫綁定
  - 等待 Vue 更新 HTML 後會執行 this.$nextTick後的程式碼
  ``` js
    methods:{
      updateMovie(newMovie){
        this.movie = newMovie
          // 網頁元素還沒實際更新
          // nextTick: 根據資料動態更新元素後
        this.$nextTick(()=>{
          ///...HTML已更新
          // 使用 jQury, TweenMax等來操作網頁
        })
      }
    }
  ```
  
### 4. 將滑鼠轉換為左右滾動
  - wheel.prevent 取得滾輪事件
  - TweenMax 處理左右動態

``` pug
  .movie(@wheel.prevent='wheel($event)')
```

``` js
  wheel(evt){
    TweenMax.to('.cards', .8, {
      left: '+=' + evt.deltaY * 2 + 'px'
    })
  }
```
網站滾動往下時是負值，往上跑時數值則是正值。
花0.8秒時間調整左距。

### 5. TweenMax.from + $event
  - $event 取得觸發事件元件位置
  - TweenMax 製作靈活動態

``` js
TweenMax.from('.buybox', 0.8, {
  left: $(evt.target).offset().left,
  top:  $(evt.target).offset().top,
  opacity: 1,
  ease: Power1.easeIn
})
```

TweenMax 結合 event.target 的應用
希望在按下當下，觸發click元件位置
利用TweenMax從A位置到B位置，並透過速度、透明度調整，看起來好像被加入到購物車的動畫。

### 6. 增強動態與使用 ICON
  - FontAwesome
  - Animate.css


圖片長寬不一致時，用Background-image會比較好。

``` pug
.cover(:style='{background-image: movie.cover}')
```
但是太長串了，改寫成 Methods的 function
``` pug
.cover(:style='bgcss(movie.cover)')
```
``` js
methods: {
  bgcss(url){
    return {
      'background-image': 'url('+ url +')',
      'background-position': 'center center',
      'background-size': 'cover' 
    }
  }
}
```

