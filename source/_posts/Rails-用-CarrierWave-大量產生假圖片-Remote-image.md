---
title: Rails 用 CarrierWave 大量產生假圖片 ( Remote image )
date: 2017-12-30 05:46:47
tags:
- Rails 5
- CarrierWave
---

我們常需要一些假資料來撐開版型、驗證，但一筆一筆新增很麻煩，所以會用到rake自訂任務請Rails一次幫忙產生500筆資料，但上傳圖片的image欄位是藉由Gem CarrierWave產生，所以需要用它提供的helper: remote-image_url


Strong Parameter 新增 CarrierWave 原生的help: remote_image_url
```
#restaurant_controller
  def restaurant_params
    params.require(:restaurant).permit(:name, :tel, :opening_hours, :address, :description, :image, :remote_image_url)
  end
```

### 自動建立 500 筆資料
新增 Rake 任務 dev.rake，自動建立 500 筆餐廳資料
在Restaurant下使用remote_image_url，在後面加上遠端網址即可。

```
Restaurant.destroy_all
Restaurant.create!(name: 'Andromeda', remote_photo_url: 'https://visualhunt.com/photos/l/7/architecture-store-building.jpg')
```
整體檔案會像是這樣，另外用FFaker Gem來產生像電話地址的假資料
```
namespace :dev do
  task fake: :environment do
    Restaurant.destroy_all

    500.times do |i|
      Restaurant.create!(name: FFaker::Name.first_name,
        opening_hours: FFaker::Time.datetime,
        tel: FFaker::PhoneNumber.short_phone_number,
        address: FFaker::Address.street_address,
        description: FFaker::Lorem.paragraph,
        category: Category.all.sample,
        remote_image_url: 'https://visualhunt.com/photos/l/7/architecture-store-building.jpg'
      )
    end
    puts "餐廳資料成功建立"
    puts "You have #{Restaurant.count} restaurants data"
  end
end
```

### 用亂數隨機讀取圖片 
但是只有一張照片好像很單調，那就改成本端檔案吧
將圖片放到public底下的目錄，檔案命名為0.jpg~20.jpg
以亂數來隨機存取圖片
```
# dev.rake
image: File.open(Rails.root.join("public/seed-img/0#{rand(1..9)}.jpg"))
```
[![](https://s3-ap-northeast-1.amazonaws.com/hazel-wordpress/wp-content/uploads/2017/12/30231913/Screen-Shot-2017-12-30-at-11.12.01-PM-1024x558.png)](https://s3-ap-northeast-1.amazonaws.com/hazel-wordpress/wp-content/uploads/2017/12/30231913/Screen-Shot-2017-12-30-at-11.12.01-PM.png)

Reference
- [How to: Upload remote image urls to your seedfile](https://github.com/carrierwaveuploader/carrierwave/wiki/How-to:-Upload-remote-image-urls-to-your-seedfile)
- [CarrierWave 图片上传和图片特殊处理](https://www.rails365.net/articles/carrierwave-tu-pian-shang-chuan-he-tu-pian-te-shu-chu-li)
