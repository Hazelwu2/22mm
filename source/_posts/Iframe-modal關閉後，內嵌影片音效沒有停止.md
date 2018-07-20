---
title: Iframe modal關閉後，內嵌影片音效沒有停止
date: 2017-12-25 06:25:40
tags:
---

### 情境介紹
> Iframe modal關閉後，內嵌影片音效沒有停止
> 內套 Bootstrap 的 modal JS特效，燈箱內容放 iframe 內崁 Youtube，但發現關閉按鈕後，影片聲音仍繼續，畫面關閉了還是有聲音。

![iframe-not-working](http://s3-ap-northeast-1.amazonaws.com/hazel-wordpress/wp-content/uploads/2017/11/11230518/e693b7e58f962.png "iframe-not-working")

#### 移除包含iframe節點
我試著用 Jquery 解決，移除整個包含iframe的節點

```
$(‘.close-modal’).on(‘click’,function(){
    $(‘.modal-body’).remove();
});
```

- 哈哈，是停止了沒錯，但好笑的是，如果user想重新開啟這個modal box就會穿幫，阿幹？阿影片勒怎麼不見了？這影片是用一次就消失喔？？？


示意圖如下

![iframe-example](http://s3-ap-northeast-1.amazonaws.com/hazel-wordpress/wp-content/uploads/2017/11/11230858/iframe-example.gif "iframe-example")

detach()：可以移除元素，但完整保留事件

```
  $(‘.close-modal’).on(‘click’,function(){
      $(‘iframe).detach();
  })
```



- 但一樣重新開啟頁面須要另寫jQ重新加回去。
- 有個更更更更更簡短的寫法，原理是抓到DOM元素後，將src重新再放入網址！就自動停了
- 這樣即使關掉頁面重開，也可以很完整呈現，也不用移除後還要再重新加入iframe


```
    $(‘.close-modal’).on(‘click’,function(){
      $(‘iframe’).attr(‘src’,
	            $(‘iframe’).attr(‘src’));
    });
```

夠簡短吧，囧！學到一課了


### 用attr設定src的屬性，取得src網址後再重新加載一次！

```
  $(‘.close-modal,button’).on(‘click’,function(){
     $(‘iframe’,).attr(‘src’,     
	      $(‘iframe’).attr(‘src’));
  });
```

同時選取兩組元素做click動作，只需要一個引號，中間用逗號隔開即可

![ifream-mode-remoe-successful-pic](http://s3-ap-northeast-1.amazonaws.com/hazel-wordpress/wp-content/uploads/2017/11/11230829/iframe-cannot-worked2-300x176.png)

#### 20171225更新
需要指定iframe取得的src連結，否則都會是拿到第一個modal的網址。
所以需要補宣告一個變數指定為當前開啟的iframe。

1. 狀態改成為當bootstrap的modal是隱藏狀態時（意味著：被使用者關閉了），將目前的iframe的網址拿掉，變空白。接著再將原有的影片網址重新塞回iframe的src屬性裡。
2. 如何取得iframe的src網址？使用attr('src')

```
$('.modal').on('hidden.bs.modal', function(){
    var $iframe = $(this).find('iframe');
    var videoSrc = $iframe.attr('src');
    console.log(videoSrc);
    $iframe.attr('src', '');
    $iframe.attr('src', videoSrc);
  });
```


嘿嘿完成圖！打開縮圖可以得到一個modal燈箱，點擊兩個按鈕都可以做到關閉的效果，真開心！超有成就感。