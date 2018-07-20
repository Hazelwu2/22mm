---
title: Rails SEO-用Sitemap-generator快速設定sitemap
date: 2017-11-04 17:19:08
tags:
---
# Rails SEO-用Sitemap-generator快速設定sitemap
如何用gem gitemap外掛，將Rails整體專案產出sitemap

``` ruby
gem 'sitemap_generator' 
# 在Gemfile加入此行
bundle install          
# 安裝套件
rake sitemap:install
自動生成 APP/config/sitemap.rb
# 開啟sitemap.rb，已自動生成好格式
SitemapGenerator::Sitemap.create do
# 必要設定default_host
SitemapGenerator::Sitemap.default_host = "http://www.你的網站.com"
# 接下來就是自由設定你自己Style的map了!
end
```


### **設定robot.txt，爬蟲能夠爬得到Sitemap**
**在public/robot.txt輸入**

```
Sitemap: http://www.你的網站.com/sitemap.xml.gz
```
### 在本端產生sitemap.xml，測試路徑是否正確

```
rake sitemap:refresh:no_ping  #測試，故不提交給搜尋引擎
localhost:3000/sitemap.xml.gz #瀏覽器
#自動下載下來後用瀏覽器重新開啟
```
### 測試沒問題了！Deploy 到 Heroku跑看看

```
git add .
git commit -am 'Added sitemap to SEO'
git push heroku master
heroku run sitemap:refresh
```
Heroku傳回console.log訊息是有建立成功，但真的測試會發現找不到sitemap。原因出在Heroku採用臨時文件系統，需要找一個容器來裝檔案才行。以下是Heroku官網說明：關於Ephemeral filesystem。

>#### Heroku 採用Ephemeral filesystem（臨時文件系統）
>當重新部署或動態管理時，導致資料會重新歸0
During the dyno’s lifetime its running processes can use the filesystem as a temporary scratchpad, **but no files that are written are visible to processes in any other dyno and any files written will be discarded the moment the dyno is stopped or restarted.** For example, **this occurs any time a dyno is replaced due to application deployment and approximately once a day as part of normal dyno management.**

## 那就放在Amazon S3吧！
>**寫入S3前，請先完成幾個步驟**
>
>1.註冊AWS會員，並登錄信用卡，才可以用Aws的免費額度。
>
>2.開啟AWS S3的服務，建立一個Bucket
>
>3.在本機環境設定先設好AWS相關的API帳密設定，接下來會用到，若沒設定好會出錯。

### 使用CarrierWave 上傳到 AWS S3
Rails含有豐富的Gem世界，Sitemaps支援多種外掛上傳到遠端空間，包含以下幾鐘：FileAdapter、FogAdapter、S3Adapter、AwsSdkAdapter、WaveAdapter，參考頁面：[官方頁面Supported Adapters](https://github.com/kjvarga/sitemap_generator#supported-adapters)
藉由Gem CarrierWave來上傳到S3，請先安裝好CarrierWave的Gem

**在sitemap.rb內修改**

```
require 'carrierwave'
require 'sitemap_generator'

SitemapGenerator::Sitemap.default_host = "你的網址"

# 你的AWS S3空間網址，創建的bucket-name
SitemapGenerator::Sitemap.sitemaps_host = "http://s3.amazonaws.com/你的bucket-name/"
# AWS S3 的bucket底下建立一個資料夾sitemaps
SitemapGenerator::Sitemap.sitemaps_path = 'sitemaps/'
# 會放在heroku伺服器內的public/tmp資料架底下
SitemapGenerator::Sitemap.public_path = 'tmp/'

# 使用WaveAdapter來上傳到AWS S3
SitemapGenerator::Sitemap.adapter = SitemapGenerator::WaveAdapter.new

SitemapGenerator::Sitemap.create do
	add '/about', :changefreq => 'monthly'
	add '/news', :changefreq => 'monthly'
	add '/article'
	Post.find_each do |post|
	    add post_path(post.friendly_id), :lastmod => post.updated_at
    end
end
```
新增home#controller，設定sitemap路徑

    rails controller -g home
    
在home_controller.rb內設定

```
  def robots                                             
	  robots = File.read(Rails.root + "config/robots.#{Rails.env}.txt")
	  render :text => robots, :layout => false, :content_type => "text/plain"
  end
  def sitemap
    redirect_to 'https://s3-ap-northeast-1.amazonaws.com/你的bucket-name/sitemaps/sitemap.xml.gz'
  end
```
在routes設定路徑

```
  get '/sitemaps.xml.gz' => 'home#sitemap'
  get '/robots.txt' => 'home#robots'
```
再重新Deploy到heroku測試看看。

``` ruby
git add .
git commit -am 'Add home#controller & Setting routes & Sitemap use Wave Adapter'
git push heroku master
heroku run sitemap:refresh
```
查看看你的AWS S3空間有沒有多出新的資料夾叫「sitemap」，有的話代表上傳成功囉！
接著就是去Google Console提交你的Sitemap啦！


#### **更新robots.txt，上傳到heroku後未抓到更改後的版本。**
在production.rb上加入後重新Deploy到heroku，就抓得到新的版本囉

     config.static_cache_control = "public, max-age=2592000"

#### 指令介紹

----------


`rake sitemap:install` 第一步安裝，會自動生成**sitemap.rb**在config目錄底下

`rake sitemap:refresh` 更新sitemap.xml.gz，並提交到搜尋引擎Google等

`rake sitemap:refresh:no_ping` 更新sitemap.xml.gz，但**不提交**到搜尋引擎（可在本機development環境下測試）

`sitemap:create`    建立空白的sitemap，未設定自動提交到搜尋引擎


### 關於連結設定
網址若是localhost:3000/welcome，則add就是設 '/welcome'
若要使用welcome_path，這種helper需要加上

```
SitemapGenerator::Interpreter.send :include, RoutingHelper
```
### 關於參數
`changefreq`：這個頁面頻繁更新的次數，預設是一星期一次
	>可設定的參數
	>'always', 'hourly', 'daily', 'weekly', 'monthly', 'yearly' or 'never'
	
`lastmod`：告訴搜尋引擎，上次修改的時間和日期，預設是Time.now
`host`：若不同網域主機需要用:host參數特別指定。預設為default_host所設定的網域
`priority`： 排名URL優先權，範圍是0到1，預設為0.5

### 關於Post（循環文章）的Sitemap

```
  add '/article'
  Post.find_each do |post|
    add post_path(post.friendly_id), :lastmod => post.updated_at
  end
```
##  


#### 參考網站

1. [Github Sitemap_generator#rails](https://github.com/kjvarga/sitemap_generator#rails)
2. [Rails SEO 第二步: 關於 sitemap](https://www.scrivinor.com/article/rails-seo-%E7%AC%AC%E4%BA%8C%E6%AD%A5-%E9%97%9C%E6%96%BC-sitemap?locale=zh-TW)
3. [Creating a sitemap with Ruby on Rails and uploading it to Amazon S3](https://www.cookieshq.co.uk/posts/creating-a-sitemap-with-ruby-on-rails-and-upload-it-to-amazon-s3)
4. [Start Your SEO Right with Sitemaps on Rails](https://www.sitepoint.com/start-your-seo-right-with-sitemaps-on-rails/)
5. [Robots.txt file on Rails heroku app not updating](https://stackoverflow.com/questions/23389609/robots-txt-file-on-rails-heroku-app-not-updating?answertab=votes#tab-top)