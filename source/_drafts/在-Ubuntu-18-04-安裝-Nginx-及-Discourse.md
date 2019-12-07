---
title: 在 Ubuntu 18.04 安裝 Nginx 及 Discourse
tags:
- discourse
---
這邊將說明如何用Discourse 版本及Nginx掛載Docker版的Discourse，可以解決每當論壇更新時，導向錯誤頁面

docker 運行後掛Port在2045上
在nginx/conf.d目錄下，新增dicouse檔案，並根據自己Docker開的Port修改
```
vi /etc/nginx/conf.d/discourse
```

discourse檔案
```
server {
    listen 80;
    server_name test.lezismore.org;
    proxy_connect_timeout       300;
    proxy_send_timeout          300;
    proxy_read_timeout          300;
    send_timeout                300;
    location ~ / {
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header Host $http_host;
        proxy_set_header X-NginX-Proxy true;
         ##這裡將 lezismore網址，導入到本機的8000port
        proxy_pass http://127.0.0.1:2045;
    }
}
```


