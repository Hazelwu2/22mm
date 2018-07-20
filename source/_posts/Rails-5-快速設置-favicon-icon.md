---
title: Rails 5 快速設置 favicon icon
date: 2018-02-10 05:13:37
tags:
- Rails 5
- Favicon Icon
---
現在的裝置上千幾種，Icon也針對各種裝置Andoroid、iOS各設置了不同大小尺寸的icon，如果在Rails內開發，可以參考以下的寫法。底下的Reference可以知道各個icon設置的用途適用於哪些裝置。

## 快速設置 Icon 步驟
1. 在view/layouts下新增`_favicon.html.erb`的樣板
2. 並在application.html.erb render `_favicon.html.erb`
3. 找icon generator 自動產生針對各裝置的icon圖
	- [Rails Generator](https://realfavicongenerator.net/favicon/ruby_on_rails#.WcpqH9MjH-a)
	- [MakeAppleIcon](https://makeappicon.com/)
4. 將 icon圖放置 `app/assets/image/icon` 內

`application.html.erb`
```
<head>
	<%= render 'layouts/favicon' %>
    <link rel="manifest" href=" <%= asset_path 'ico/manifest.json' %>">
    <meta name="msapplication-config" content="<%= asset_path 'ico/browserconfig.xml' %>">
    <meta name="apple-mobile-web-app-title" content="xxxx">
    <meta name="application-name" content="xxxx">
    <meta name="msapplication-TileColor" content="#2b5797">
    <meta name="theme-color" content="#ffffff">
</head>
```

`_favicon.html.erb`

```

  <% %w(57 60 72 76 114 120 144 152 180).each do |s| %>
      <%= favicon_link_tag "ico/apple-icon-#{s}x#{s}.png", rel: 'apple-touch-icon', type: 'image/png', sizes: "#{s}x#{s}" %>
  <% end %>

  <% %w(36 48 72 96 144 192).each do |s| %>
      <%= favicon_link_tag "ico/android-icon-#{s}x#{s}.png", rel: 'android-chrome', type: 'image/png', sizes: "#{s}x#{s}" %>
  <% end %>

  <% %w(70 144 150 310).each do |s| %>
      <%= favicon_link_tag "ico/ms-icon-#{s}x#{s}.png", rel: 'mstile', type: 'image/png', sizes: "#{s}x#{s}" %>
  <% end %>

  <% %w(16 32 96).each do |s| %>
      <%= favicon_link_tag "ico/favicon-#{s}x#{s}.png", rel: 'x-icon', type: 'image/png', sizes: "#{s}x#{s}" %>
  <% end %>

  <%= favicon_link_tag 'ico/favicon.ico', rel: 'shortcut icon' %>
  <%= favicon_link_tag 'ico/favicon.ico' , rel: 'bookmark' %>
  <%= tag(:link, rel: 'manifest', href: ('ico/manifest.json')) %>
```

#### Apple Touch Icons

- 120x120: iPhone Retina (iOS 7)
- 180x180: iPhone 6 Plus (iOS 8+)
- 152x152: iPad Retina (iOS 7)
- 167x167: iPad Pro (iOS 8+)


```
<link rel="apple-touch-icon" href="older-iPhone.png"> // 120px  
<link rel="apple-touch-icon" sizes="180x180" href="iPhone-6-Plus.png">  
<link rel="apple-touch-icon" sizes="152x152" href="iPad-Retina.png">  
<link rel="apple-touch-icon" sizes="167x167" href="iPad-Pro.png">
```

#### IE11 微軟動態磚 
取得 browserconfig.xml 檔案

```
<meta name="msapplication-config" content="ieconfig.xml" />
```

### Reference | 參考資料

- [建立自訂動態磚 - Microsoft](https://msdn.microsoft.com/zh-tw/library/dn455106(v=vs.85).aspx)
- [Favicon – 關於我的最愛圖示的二三事](http://pressing.space/frontend/favicon-trivia/)
- [Here's Everything You Need to Know About Favicons in 2017](https://sympli.io/blog/2017/02/15/heres-everything-you-need-to-know-about-favicons-in-2017/)
- [Favicon Generator for Ruby on Rails 自動生成](https://realfavicongenerator.net/favicon/ruby_on_rails#.Wn0XVJP1Usn)
