---
title: 做個超 AI 的響應式圖片吧，利用 HTML5 srcset 搭配 Rails Helper img_set_tag
date: 2018-02-17 05:04:50
tags:
- Rails 5
- RWD
---
做 Responsive Web Design 時難免少不了對圖片的處理，圖片能彈性隨著螢幕裝置大小調整，有多種做法可以參考。今天來探討使用 HTML5 新增的屬性 srcset 來搭配使用 Rails 的 Helper

通常做響應式圖片有兩種作法
1. 使用同一張圖片，依照容器、螢幕尺寸放大縮小
2. 偵測不同的裝置，根據螢幕大小，使用不同大小的圖片

### 作法一
作法一：重頭到尾都使用同一張圖片，可以跟著螢幕修改圖片的比例大小
> 響應式圖片 建議設定max-width: 100%
確保圖片不會超過畫面的大小，父層的容器設多少大小，子層的圖片就會乖乖在他背後下貼著容器。
```
img { max-width: 100%; height: auto}
```
> 缺點：圖片量若很大，載入頁面時間會加長，對於行動裝置上網的客群，可能會因為網站載圖片跑太慢，導致用戶不想等直接離開網站。

### 做法二

做法二： 隨著裝置提供不同大小的圖檔
> 我有一張圖片、但是幾個不同大小的版本想要替換使用
> 小螢幕裝置，就下載比較小的圖片，減少行動裝置上不必要的寬頻浪費
> 大螢幕就下載比較大的圖片、解析度也較高、看得比較清楚

優點：可以根據圖片顯示的實際需要，給用戶端剛剛好的大小版本
> 需要注意的事項：查詢[Can I Use](https://caniuse.com/#search=srcset) 確認主流瀏覽器支援狀況

#### 偵測不同裝置隨意換上圖片
偵測不同裝置隨意換上圖片，有以下幾種做法

1.  使用HTML5 `<img>` 的屬性 `srcset`
缺點：需要到Can I Use 看各主流瀏覽器版本是否已支援
2. HTML5 `<picture>`
3. `使用 srcset + picture`
4. 用 JavaScript 監聽 `window.resize`事件，修改圖片路徑
5. 使用 `svg` 向量圖
6. 使用第三方外掛，例如：[picturefill](https://scottjehl.github.io/picturefill/)來補足不支援picture的瀏覽器


### 狀況劇一
狀況劇一：設計師提供給我手機版、桌機版裝置的兩種圖片，要怎麼切圖搭配srcset使用？

#### 先切圖
1. 給 Mobile 小螢幕裝置的圖片
切成 1x 給一般手機
切成 2x 給 Retine 高畫質螢幕
2. 給tablet, desktop 大螢幕裝置的圖片
切成 1x, 2x, 3x

#### 使用 srcset 

```
<img srcset="360.jpg 360w,
768.jpg 768w,
1200.jpg 1200w,
1920.jpg 1920w"
sizes="
(max-width: 360px) 100vw,
(max-width: 768px) 90vw,
(max-width: 1980px) 80vw,
768px"
src="360.jpg" alt="範例">
```
> srcset 非常的簡單，寫下你要放上的幾張圖片和他的寬度，瀏覽器就會自動去選擇
> srcset 分別跟瀏覽器先生說：「嗨，我這裡有360.jpg、768.jpg、1200.jpg、1920.jpg圖片」
> sizes 也和瀏覽器說：「不同裝置的寬度(media query)下，圖片的寬度也要幫我個別調整！」

```
sizes="(max-width: 360px) 100vw"
```
在320px的iphone4下瀏覽，圖片寬度自動也會被調整為 100vw。

### Rails img secret, sizes Helper
由於Rails內建的Helper沒有`image srcset`，於是我們要手動新增Helper
```
// 新增在 application_helper.rb
def image_set_tag(source, srcset = {}, options = {})
  srcset = srcset.map { |src, size| "#{path_to_image(src)} #{size}" }.join(', ')
  image_tag(source, options.merge(srcset: srcset))
end  
```

新增完後便可以照著他的規則使用啦！剛剛切出來的五張圖片可以派上用場囉
```
<%= image_set_tag 'How-we-work.png', { 
  'How-we-work-Mobile.png' => '320w',
  'How-we-work-Mobile@2x.png' => '640w',
  'How-we-work-PC@2x.png' => '1024w',
  'How-we-work-PC@3x.png' => '1980w'
}, alt: '如何運作', class: 'img-fluid mx-auto d-block' %>
```
瀏覽器吃掉上述程式碼後，它會通過 srcset 屬性自動選擇 Mobile 2x,  PC 2x 等圖，比如說今天用 iPhone 6s，便會自動選擇 Mobile@2x 的圖。

> `default`, `image_set_tag 'How-we-work.png'` 這裏即指 default

若瀏覽器太舊看不懂 srcset，會讀取 src 的圖片網址來顯示。套上範例，瀏覽器不支援就會讀取`How-we-work.png`的圖檔

> 以下的 `w` 不是真的指 `px` ，而是`裝置像素比`，為了好理解先套上px
- `320w`
手機模式瀏覽 src自動轉換成 Mobile.png
當偵測到瀏覽器為不大於 320px 寬時，顯示 Mobile.png 這張圖
- `640w` 
平板模式瀏覽 src自動轉換成 Mobile@2x.png
- `1024w`
平板裝置瀏覽 寬度有1024，src自動轉換成 PC@2x.png
- `1980w`
桌機裝置瀏覽 寬度有1980，src自動轉換成 PC@3x.png

### w是什麼？
W 是 設備像素比(DevicePixelRatio)，詳細可點擊[查詢各裝置的裝置像素密度](http://screensiz.es/)
320w, 640w, 1024w, 1980w 這裡的w不是指px，而是「設備像素比」 也就是 `window.devicePixelRatio`

### 測試
如何測試實驗圖片真的有跟著寬度做更換？
建議測試時開啟Chrome無痕視窗，以避免瀏覽器快取後，不會自動切換圖片

1. 打開Chrome 開發人員工具
2. 指到 src 
3. 會發現瀏覽器視窗隨著調整，src指定的圖片路徑會變成設定的圖檔
> 像是我開到手機寬度時，src指定圖片路徑會變成Mobile.png，開到正常電腦大小寬度時，src的路徑則會變成PC@3x.png

4. 大功告成！

### Reference | 參考資料
- [為什麼低解析度的JPG會跟馬賽克一樣？](http://www.dailycold.tw/7216/jpeg-jpg-lossy-compression-artifacts/)
- [Using image_tag with srcset attribute?
](https://stackoverflow.com/questions/45360050/using-image-tag-with-srcset-attribute)
- [响应式图片实战](https://segmentfault.com/a/1190000008662857)
- [Picasa 圖床的獨家應用：製作 RWD 自適應圖片，讓手機自動載入小圖，加快網頁讀取速度](https://www.wfublog.com/2017/08/picasa-rwd-image-srcset.html)
- [RWD響應式網頁專案開發心得(一)](http://lauraluo.github.io/2015/04/15/20150415-RwdPojectStudy/)
- [用 srcset 屬性做簡單的 Responsive Image](http://blog.zhusee.in/post/248199/basic-responsive-image-with-srcset-property)
- [《Picturefill 自適應圖片》隨著裝置提供不同大小的圖檔(JS版)](https://www.minwt.com/webdesign-dev/js/12386.html)
- [查詢各裝置的裝置像素密度](http://screensiz.es/)