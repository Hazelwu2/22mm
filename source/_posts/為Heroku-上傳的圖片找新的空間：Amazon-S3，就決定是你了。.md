---
title: 為Heroku 上傳的圖片找新的空間：Amazon S3，就決定是你了。
date: 2018-07-23 23:11:59
tags:
---
我用Ruby on Rails寫了一個CRUD網路相簿的網站，主機採用Heroku提供的服務。但是有一個缺點，Heroku不提供檔案儲存的空間，每過一段時間就會洗白一次。
講白話一點就是：我上傳的相片過一段時間就會不見了，我得為我的相片找新的空間存放才行。這裡使用的是Amazon S3 storage服務，可以自動上傳到S3空間

- `20180723` 更新，Amazon 有更改介面，故 S3 Access key指引教學也有更新內容。

----------
廢話不多說，開始教學吧
### 教學開始
#### 安裝Gemfile
`gem "fog-aws”`
`bundle install`

#### 在Amazon S3新增一個新的backet
我命名為photo-album-hazel，並設定在日本東京的位置。

#### 新增目錄 lib/carrierwave/storage/fog.rb

```
//在fog.rb貼上
CarrierWave.configure do |config|
  config.fog_provider = 'fog/aws' 
  if Rails.env.production?
    config.fog_credentials = {
      provider:              'AWS',
      aws_access_key_id:     ENV['S3_KEY'],
      aws_secret_access_key: ENV['S3_SECRET'],
      region:                'ap-northeast-1'
    }
    config.fog_directory  = ENV['S3_BUCKET']
  else
    config.storage :file
  end
end
```

在Uploader設定儲存檔案的方式，若在 production 環境下， 將會透過 fog 上傳檔案到 AWS 的 bucket; 若是開發環境， 則會將檔案放在本地端的 public 目錄下。


```
# photo-image-uploader.rb設定
  if Rails.env.production?
    storage :fog
  else
    storage :file
  end
```


#### AWS S3 KEY 要去哪裡找？
*20180723 更新，Amazon 有更改介面，更新要怎麼從 Amazon 找到 S3 Access key*
1. AWS 登入後
2. 點開選單，點擊 `Security Credentials`
![S3 Security Credentials](https://i.imgur.com/efIyId3.png)
3. 側邊選單點 Users，並建立一個 User 帳戶
4. 點擊 `你剛建立的User帳號`，標籤選 `Security Credentials`
5. Create Access Key
![20180723 update. how to find Amazon S3 access key](https://i.imgur.com/B4DTXAi.png)
在 Create Access Key的過程便會 Show出 ID 與 KEY 囉。

#### Region設定方式查詢：[查詢AWS區域代號](http://docs.aws.amazon.com/general/latest/gr/rande.html "查詢AWS區域代號")

| Region Name    |     代稱 |   Amazon Route 53 Hosted Zone ID*   |
| :-------- | --------:| :------: |
| Asia Pacific (Tokyo)     |   ap-northeast-1  |  apigateway.ap-northeast-1.amazonaws.com   |

我設定的bucket在東京：所以Region是ap-northeast-1


#### 用環境變數方式儲存，heroku與本機設定KEY
How to setting localhost & Heroku environment variable ?
總共有三項要設定，請分別設定在本機、Heroku上
1. Bucket Name，像我在S3建立的bucket name就是photo-album-hazel
2. KEY ID
3. KEY SECRET

本機 Rails 環境變數設定 AWS S3 KEY

```
export S3_KEY= "your access key id"
export S3_SECRET= "your secret access key"
```
範例程式碼如下，請不要複製，皆為虛假，請真正填上你S3的資訊。
```
export S3_KEY='AKIAI2XXX24DGFCDNCQ'
export S3_SECRET='nZZ5JhyVjfKyMC5GD9gGMERLE3j3!XroZsEjLK!T'
export S3_BUCKET='restaurant-review'
```

### Heroku 遠端設定 AWS S3 KEY
若沒有在heroku上設定S3 KEY，直接`git push heroku master`推到heroku上，會發現推不上去哦！
錯誤訊息會這樣顯示
> ArgumentError: Missing required arguments: aws_access_key_id, aws_secret_access_key
> Precompiling assets failed.
所以還是要乖乖的先把這步驟做完，遠端連線到Heroku Server並設定好Key後，即可順利推上去。


`$ heroku config:set S3_KEY=xxx S3_SECRET=yyy S3_BUCKET=zzz`
以下程式碼僅為參考用，非正確可使用的KEY。
```
heroku config:set S3_KEY=AKIAI2XXX24DGFCDNCQ
heroku config:set S3_SECRET=nZZ5JhyVjfKyMC5GD9gGMERLE3j3!XroZsEjLK!T
heroku config:set S3_BUCKET=restaurant-review
```
![Setting-AWS-S3-loaclhost](https://s3-ap-northeast-1.amazonaws.com/hazel-wordpress/wp-content/uploads/2017/12/23163331/setting-aws-s3-1024x356.png)



#### 設定好了就可以git上傳囉，在heroku上測試是否有成功上傳到S3 bucket吧

```
git add .
git commit -am 'Setting carrierwave upload to AWS S3'
git push
git push heroku master
```

Push到Heroku後，出現錯誤了。
查看heroku logs才知道：Rails找不到Carrierwave的常數fog

```
/app/vendor/bundle/ruby/2.3.0/gems/carrierwave-1.2.0/lib/carrierwave/uploader/configuration.rb:78:in `eval': uninitialized constant CarrierWave::Storage::Fog (NameError)
```
嘗試解決方法
1. Gem 安裝 'fog'
結果：無效
2. 在 lib/carrierwave/storage/fog.rb 多加上一行程式碼
`require 'carrierwave/storage/fog'`
結果：無效
3. 把 lib/carrierwave/storage/fog.rb 刪除，並在 config/initializer下新增 carrierwave.rb
把fog.rb的內容複製過去
（當初直接新增fog.rb是完全照著官方文件做的說...）
結果：成功，Heroku可以成功開啟網站了（哭）

可以順利開啟網站後，開Chrome開發人員工具檢查吧，看是否有上傳到AWS S3上

![Successful to upload to AWS S3 using Rails](https://s3-ap-northeast-1.amazonaws.com/hazel-wordpress/wp-content/uploads/2017/12/23163310/s3-sample.jpg)
成功啦！！

#### Reference
1. [CarriveWave Github 官方文件](https://github.com/carrierwaveuploader/carrierwave#uploading-files-from-a-remote-location)
2. [使用Carrierwave上傳到AWS S3](http://tsaith.github.io/shi-yong-carrierwave-shang-chuan-dang-an-dao-aws-s3.html)
3. [使用 Carrierwave 處理檔案上傳 (整合 imagemagick 與 Amazon S3)](http://rubyist.marsz.tw/blog/2012-01-10/carrierwave-guides-with-amazon-s3-and-imagemagick-integration/)
