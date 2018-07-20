---
title: 複製 Production 真實環境資料，模擬超逼真測試 | Rails 5
date: 2018-06-13 03:23:39
tags:
- scp server
- production data into local
- copy production data transfer to staging
---
為了擬真測試，我們往往需要更真實的數據，採取的作法是：將 Production 真實性的資料庫複製一份至 local 端測試，本文採取直接用Postgresql操作，並紀錄當下遇到的問題及解決辦法。

### 將要測試的資料庫（例如：production）匯出成sql
將原始production資料庫:database_name，匯出成「database_name-20180612.sql」檔案
> （註：通常會加上日期，會較為清楚知道這是什麼時期匯出的資料）

你需要知道：Server端上的database_url、Server的IP及資料庫的名稱
格式大約會像是這樣：

你可以從Google Cloud Platform上查詢或是在Server租用的主機商查看資訊
```
database_url
範例格式：postgresql://資料庫使用者:資料庫密碼@ServerIP/database_name
真實格式：postgresql://hazelwu:123456@11.12.200.205/production
```
如果不清楚database_url是怎麼生成的，可以參考這篇文章：[Rails Guide Configure database](http://edgeguides.rubyonrails.org/configuring.html#configuring-a-database)


遠端連上Server後
ssh hazelwu@11.12.200.205
移動到專案目錄後，在Commend Line上輸入，並自行填上相關參數
```
pg_dump -h 11.12.200.205 -U 資料庫使用者 資料庫名稱 > production-20180612.sql
## 範例：使用者 hazelwu, 資料庫名稱 123456
pg_dump -h 11.12.200.205 -U hazelwu 123456 > production-20180612.sql
```

`pg_dump dbname > dumpfile`
- 原始指令：pg_dump 資料庫名稱 > 匯出檔案名稱
- 參數意義：
	-h：host
	-U：user
	-p：port（註：專案未有設定port，所以不需要設定此參數）
> 由於是在Server端要連線至資料庫（本文撰寫時採用GCP），所以需要特別輸入IP位址及User名稱，才能有權限讀取資料庫，並匯出sql檔案。

輸入完指令後，便會將 Porduction的資料庫檔案，匯出成sql檔。
接著要做的就是把這個新生成的檔案（production-20180612.sql），從Server拉到自己Local端

不過，通常資料庫都會越來越肥大，在拉到Local端之前要記得先用gzip壓縮一下哦。

### 在Server上 gz 壓縮 | 越來越肥的資料庫
以下操作指令皆為在`Server`端輸入。

1. 將檔案壓縮成gz
解壓縮完便會還原成 production-20180612.sql
```
$ gzip production-20180612.sql
```
2. gz解壓縮指令
```
$ gunzip production-20180612.sql.gz
```


### Remote Server 下載 sql 到 Local端
要如何從Server端直接下載檔案到本機電腦呢？
有幾種方式：curl、wget或是scp都可以。
- 使用wget的話，需要將檔案放在server上的開放空間，且必須要是瀏覽器可以打開的網址，例如

```
$ Wget url
wget http://11.12.200.205:8000/production-20180612.sql
```

以下是採取`scp`方式
scp其實很好記，顧名思義：Server Copy，夠白話了吧XD
- 指令格式
```
scp user@server:/路徑/你要/下載/的檔案 ~/Desktop/你要下載/到/電腦/的哪個/位置
scp hazelwu@11.12.200.205:/home/hazelwu/test/current/production-20180612.sql ~/Desktop/github/test_rails
## 這樣便會將production-20180612.sql下載至desktop/github/test_rails資料夾內。
```
#### 你知道嗎？其實還能夠再簡化一點
指定你要下載的東西，在Commend Line上先移動到想要把檔案放的資料夾（例如/tmp），接著後面那串指令（表示本機位置）以`.`來取代就可以了，便會將檔案下載到`/tmp`內。
> 建議可以直接放在專案內的tmp內，因為不會被commit進去、tmp主要都為放暫時性檔案使用。（通常gitinore有加入/tmp/*）

打以下的指令時，可以先在Command Line移動到你想要複製的目錄，後面就不用打一長串的東西囉

```
### 簡化後的版本
[local端] cd tmp/
scp hazelwu@11.12.200.205:/home/hazelwu/test/current/production-20180612.sql .
```
這樣即可將sql匯出來哩

### 保個保險吧：攔截會發送給使用者的動作（Email)
由於這些資料都非常真實，是來自真實使用者的資料，包含信箱手機等都為真的
所以不能真的做出通知使用者的行為，例如 E-Mail 寄送、簡訊認證碼送出等。

Rails 有提供 `register_interceptor`攔截 Email

1. config/initializers/email.rb

```
# 新增 config/initializers/email.rb 
# 在 Rails 啟動時，如果是非 production 環境就載入EmailInterceptor：

unless Rails.env.production?
  puts "阻攔Email，將收件人皆改為test測試用信箱"
  require 'email_interceptor'
  ActionMailer::Base.register_interceptor(EmailInterceptor)
end
```
2. lib/email_interceptor.rb新增以下程式碼

```
## 新增 lib/email_interceptor.rb 並且自訂條件：將收件人信箱皆改為測試用信箱
class EmailInterceptor
  def self.delivering_email(message)
    message.to = ['test@example']
  end
end
```

### 將 sql 匯出到 local端 / develop environment
順利抓下Server端的資料後，也設上攔截機制了，接著可以匯入資料囉。

- 匯出資料庫
  pg_dump database_name > backup.sql
- 匯入資料庫
  psql db_name < backup.sql
  
還記得剛剛匯出的指令嗎？
`pg_dump -h 11.12.200.205 -U hazelwu 123456 > production-20180612.sql`
接著要匯入的話，指令要這樣打
`psql 123456 < production-20180612.sql`
輸入完之後，可以進入到Console.log試試看資料庫有沒有匯入成功
> 有發現為什麼突然少那麼多參數嗎？
- 原因
	由於目前環境是Local端，不需要填入帳號密碼等參數來藉由GCP連入資料庫了，我們只要指定檔案，匯入到本機的資料庫即可。預設是沒有輸入參數，他會抓當前的IP位置
	
- 重整 Postgresql 匯入/匯出資料庫指令
```
匯出資料庫
psql project_development < project_production-20180612.sql
匯入資料庫
psql project_development < project_production.sql
```

### 實際操作時，遇到的問題：
#### 1. 從Server端抓下來的User data，欄位上並沒有實際的密碼，導致會失敗，User Model做驗證：導致密碼效驗不能為空白，有什麼辦法可以自動化加上自設的密碼？

> 在 Server端 用 Gem 產生資料檔 Seed，抓下來到Local端跑種子檔，
> 結果：無法與關聯資料庫對上，關聯id對不上去（如：user_id會對不上，找不到該User）

資料格式類似如下
```
  User.create!([
  {email: "fduwsbxcbkdym4jzxn6b@gmail.com", encrypted_password: "zxkdfgjeute123ds", reset_password_token: nil, reset_password_sent_at: nil, remember_created_at: nil}
```
第一次試著做時，是用`Gem 'seed_dump'`在Server端上做，因屢次失敗的原因，後面便不採用seed_dump的做法了。轉移production資料到local，直接用postgresql指令直接拉，會比較快。


#### 2. 在Rails 5 上重新刪除資料，並再建立一次，有辦法讓id重新從0開始跑嗎？
將local端資料庫全部刪除，打算跑rails db:seed:users，將production轉移至local
但發現會對不上關聯資料庫的部分，因為重建立的user_id並不是從0開始，這該怎麼辦？

- 刪除資料後，id會從0開始有下列幾個做法
1. TRUNCATE table `TABLE_NAME`; 
2. DROP table `TABLE_NAME`;
3. rails db:drop

- `TRUNCATE` 與 `DROP` 兩者的差別在於
	- 前者是把資料欄位全部刪除；
	- 後者 DROP 則是把整個 TABLE 刪除

#### 3. Server上操作postgresql時，準備匯出sql檔，發現Server的資料庫與專案上的資料庫版本不一致，導致無法順利匯出
由於Server端的資料庫與專案上安裝的資料庫版本不符（一個為9.6版本、一個則是9.5版本），導致無法匯出
- 一開始的嘗試：
先在Server上安裝相同版本的psql，但未能成功，仍還是舊版本QQ
搞版本總是很複雜又很麻煩，不過有一個比較快的方法是：

在跑`pg_dump`指令時，prefix前面加上指定的版本，就能夠指定為9.6版本跑pg_dump的指令，而不是舊的版本（9.5)
- 如何解決：
因此我們需要指定版本：`/usr/lib/postgresql/9.6/bin/pg_dump` 後面再加上原要接的參數，就可以輕鬆搞定版本問題啦！

```
# 原本psql匯出指令
pg_dump -h 11.12.200.205 -U hazelwu 123456 > production-20180612.sql 
# 解決版本不一致 匯出指令
/usr/lib/postgresql/9.6/bin/pg_dump -h 11.12.200.205 -U hazelwu 123456 > production-20180612.sql 
```


### Reference | 參考資料
- [How to Download a File from a Server with SSH /SCP ](http://osxdaily.com/2016/11/07/download-file-from-server-scp-ssh/)
- [Rails 複製資料到 development環境](http://motion-express.com/blog/20141112-rails-test-db-seed)
- [Postgresql Dump](https://www.postgresql.org/docs/10/static/backup-dump.html)
- 小蟹本人XD
