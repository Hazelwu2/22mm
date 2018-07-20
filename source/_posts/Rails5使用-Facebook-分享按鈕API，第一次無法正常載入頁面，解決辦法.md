---
title: Rails 5使用 Facebook 分享按鈕API，第一次無法正常載入頁面，解決辦法
date: 2017-11-08 06:18:55
tags:
- Rails 5
- API
---
# Rails 5 使用 Facebook 分享按鈕 API ，第一次無法正常載入頁面，解決辦法
在進行一個案子時遇到的困難：引用臉書的分享按鈕 API，第一次載入頁面時不會出現，第二次載入頁面後才會出現，甚至要等候很久才能出現。   

### 問題分析   
    
  - 推測出有可能是turbolinks造成的問題，但jquery已有寫當`.on(turbolink:load)`了，後來從Stackflow找到解答    
  - 請將下方程式碼複製貼上，就即可解決問題了！ 
  
```
<script>
  $(document).on("turbolinks:load", function(){
      (function(d, s, id) {
      var js, fjs = d.getElementsByTagName(s)[0];
      if (d.getElementById(id)) return;
      js = d.createElement(s); js.id = id;
      js.src = 'https://connect.facebook.net/zh_TW/sdk.js#xfbml=1&version=v2.10&appId=2046979441994437';
      fjs.parentNode.insertBefore(js, fjs);
    }(document, 'script', 'facebook-jssdk'));
  })

(function($) {
  var fbRoot;

  function saveFacebookRoot() {
    if ($('#fb-root').length) {
      fbRoot = $('#fb-root').detach();
    }
  };

  function restoreFacebookRoot() {
    if (fbRoot != null) {
      if ($('#fb-root').length) {
        $('#fb-root').replaceWith(fbRoot);
      } else {
        $('body').append(fbRoot);
      }
    }

    if (typeof FB !== "undefined" && FB !== null) { // Instance of FacebookSDK
      FB.XFBML.parse();
    }
  };

  document.addEventListener('turbolinks:request-start', saveFacebookRoot)
  document.addEventListener('turbolinks:load', restoreFacebookRoot)
}(jQuery));
</script>
```