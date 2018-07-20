---
title: 神奇的migration 生成器 | Ruby on Rails 5
date: 2018-07-20 14:45:18
tags:
- migration
- rails
---
## 神奇的migration 生成器 | Ruby on Rails 5

參照著格式便可以自動產生Migration檔（包含格式與欄位及型態）

```
rails g add_xxx_to_資料表 [field:type][:index]
rails g remove_xxx_to_資料表 [field:type][:index]
```
同時可以設定我們需要的欄位，
我想要新增一個fee欄位到users的Table下

> 你可以想像成一個Excel表，這個Excel Table叫做Users，
> 因為Excel表有很多資料欄位(field)，所以都要用複數來表示，也就是Users
> 按照Rails的慣例，Table總表以複數來表示。
```
rails g migration add_fee_to_users fee:integer:index
```

在後面加上:index則是代表要幫這個資料欄位`fee`加上索引
加上索引是為了避免產生太多的查詢，像是query+1效能問題

### 怎麼判斷這個資料欄位(field)要不要加上索引？
可以參考幾個點，但主要還是要看這個欄位`fee`是否會是需要常查詢的欄位？
1.  會被查詢的欄位（使用where)
2.  會被group的欄位(使用到group方法)
3.  會被排序的欄位(使用到order)
4.  外部鍵foreign key

他便會自動產生Migration檔：時間戳記_add_fee_to_users
註：時間戳記會根據現實的時間自動產生，像是20180523051928這種形式
```
class AddeeToLoans < ActiveRecord::Migration[5.1]
  def change
    add_column :users, :fee, :integer
    add_index :users, :fee
  end
end
```
### 後悔產生migration檔，如何手動刪除？
注意：這個刪除的前提是你還沒有跑migration，也就是還沒真的新增此欄位到資料庫裡
剛剛生成的指令是輸入

```
rails g migration add_fee_to_users fee:integer:index
```

那麼刪除就只要將g改為d就可以囉！

```
rails d migration add_fee_to_users fee:integer:index
```


