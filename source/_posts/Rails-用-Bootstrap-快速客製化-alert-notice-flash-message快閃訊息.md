---
title: Rails 用 Bootstrap 快速客製化 alert & notice flash message快閃訊息
date: 2017-12-29 05:49:06
tags:
---
![卡了一條很醜的紅色alert訊息](https://s3-ap-northeast-1.amazonaws.com/hazel-wordpress/wp-content/uploads/2017/12/29113303/Screen-Shot-2017-12-29-at-11.29.32-AM.png)
一般來說，需要使用會員系統會用Gem來安裝Device的套件，但可苦惱了，內建的flash message都會卡在上面，即使套上bootstrap的客製化，加上alert-info等的class，它仍會卡在頁面最上端，有一條長長橫橫的而且很醜，不會自動消失，這時候該怎麼辦呢？

```
# 上述範例圖片的程式碼
<% alert %>
<% notice %>
```

很簡單，我們多設一個條件式：如果alert或notice的flash訊息提醒出現，則幫我套上bootstrap的樣式。
bootstrap有幫我們客製化一個樣式，可以點擊Ｘ來關閉flash message，必須在`button`的屬性標籤裡確保有`data-dismiss=“alert"`才可以正常運作。

```
<% if alert %> 
    程式碼
<% end %>
```

程式碼範例：

```
<% if alert %>
  <div class="alert alert-warning alert-dismissible" role="alert">
    <button type="button" class="close" data-dismiss="alert" aria-label="Close">
    <span aria-hidden="true">×</span></button>
    <strong>Warning!</strong><%= alert %>
  </div>
 <% end %>
 <% if notice %>
   <div class="alert alert-info" role="alert">
     <button type="button" class="close" data-dismiss="alert" aria-label="Close">
     <span aria-hidden="true">×</span></button>
     <%= notice %>
   </div>
 <% end %>
```

#### Rails 安裝 Bootstrap
等等？可是我不知道Rails怎麼安裝 Bootstrap
請看此篇文章：「教你在Rails 快速安裝 Bootstrap，快速打造有質感的頁面」（仍還在寫中XD）
#### 完成圖
套上Bootstrap button, CSS樣式效果後，變得更方便，也不會卡住一條在頁面最上端，同時覺得礙眼的話還可以直接點擊X關掉flash message！
![迅速又方便](https://s3-ap-northeast-1.amazonaws.com/hazel-wordpress/wp-content/uploads/2017/12/29113320/Screen-Shot-2017-12-29-at-11.23.25-AM.png)

### Reference
-  [Bootstrap Alert Message 官方文件](https://getbootstrap.com/docs/3.3/components/#alerts-dismissible "Bootstrap Alerts官方文件")
-  [Bootstrap components added for alerts and notices](https://medium.com/coding-and-web-development/bootstrap-components-added-for-alerts-and-notices-26b0048974ac)