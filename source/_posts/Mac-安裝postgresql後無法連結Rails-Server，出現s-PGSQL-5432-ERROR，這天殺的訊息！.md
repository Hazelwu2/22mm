---
title: Rails Server 無法連結 Postgresql，出現s.PGSQL.5432 ERROR，解決辦法
date: 2018-04-09 03:59:09
tags:
- rails server
- s.PGSQL.5432
- Rails 5
- psql
---

### s.PGSQL.5432是蝦咪挖溝？
紀錄在 Mac 跑Rails Server時使用PG資料庫，出現s.PGSQL.5432 ERROR，我是如何解決的。
這次遇到的狀況是要跑`rails db:migrate`時出現錯誤訊息，追根究底時發現有可能原因是出自於psql資料庫，於是在終端機輸入`psql`出現ERROR訊息：
RUN版本：`postgresql 10.3`

```
psql: could not connect to server: No such file or directory
    Is the server running locally and accepting
    connections on Unix domain socket "/var/pgsql_socket/.s.PGSQL.5432"?
```
上述錯誤訊息是在說：現在開啟的Rails Server沒有與本機的psql資料庫連線，你要不要再確認一下？

造成此原因有很多種，但最白癡的原因莫過於本端沒有打開psql服務

> 若你也有出現此錯誤訊息，可以先測試看看是不是根本沒有打開psql服務，若仍無法排除，再嘗試重新裝載psql。
> `$ pg_ctl -D /usr/local/var/postgres start`


## 解決良藥
### 1. 本機開啟psql的服務
如果psql本身被關閉的話，輸入以下指令便會告知你psql服務被打開囉。
Mac若重新開機的話，psql服務會自動被關閉，通常遇到無法連線local psql時輸入此指令皆可解決。
`$ pg_ctl -D /usr/local/var/postgres start`

### 2. 重新裝載psql
如果本機確認有打開psql服務的話，仍無法排除狀況，那就重新裝載psql吧QQ。

- 先移除前一個版本Postgresql
`$ brew uninstall --force postgresql`
- 確保有刪除乾淨，再次手動刪除
`$ rm -rf /usr/local/var/postgres`
- 用Homebrew 安裝 Postgres
`$ brew install postgres`
開啟PostgreSQL的Server，在終端機輸入：
`$ pg_ctl -D /usr/local/var/postgres start`
- 建立資料庫
`$ initdb /usr/local/var/postgres`
或是直接在Rails專案建立資料庫
`$ rails db:create`

### 備份資料庫
如果local端有舊的資料庫，也並不打算轉移的話...
刪除舊有資料庫
```
rm -r /usr/local/var/postgres
```

重新建立一個新的
```
initdb /usr/local/var/postgres
```
新建新的資料庫
`createdb 資料庫名稱`




## Reference 
- [PostgreSQL and PostGIS installation in Mac OS.](https://medium.com/@Umesh_Kafle/postgresql-and-postgis-installation-in-mac-os-87fa98a6814d)