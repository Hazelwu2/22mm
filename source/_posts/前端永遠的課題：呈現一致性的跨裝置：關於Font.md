---
title: 前端永遠的課題：字體－呈現一致性的跨裝置
date: 2018-03-18 04:25:39
tags:
- 
---
前端最主要就是將所有裝置能一致化顯示，今天來談談「字體」的部分
大家都知道Windows, Linux, Mac三大作業系統，想當然也有其系統的內建字體，若只指定常見的微軟正黑體，其他作業系統沒有該字體，就會顯示該作業系統的預設字體，而不是微軟正黑體，這樣就沒有超能力隨心所欲控制字體了，這怎麼行！
身為一位專業的前端工程師，是有必要了解網頁字體的，Okay, Let’s go.

> 要能隨自己心念呈現想要的字體，首先要先理解「字體擺放的順序」，順序超重要的啊

## 字體通用順序
英文字型 > Linux系統 > Mac系統 > Windows > 基礎字體

### 設定 font-family 到底是中文在前面、還是英文啊？

> 英文字型在前，中文字型在後

如果中文字型在前，那麼網頁會永遠讀取不到Arial的字體，正確示範應為以下設定
```
	font-family: "Arial", "微軟正黑體"
```

## 使用機率較少的字體放前，使用機率高的字體放後
- 使用機率較少的字體放在前面（例如只有Mac系統才有的蘋果儷中黑字體）
- 使用機率高的字體放在後面（幾乎每個作業系統都有的新細明體或標楷體）


> 舉例來說，如果我想要瀏覽這個網站的使用者，使用Mac裝置看得是蘋果儷中黑，用Windows瀏覽則看到的是微軟正黑體，那該怎麼設定？

```
	font-family: "蘋果儷中黑", "微軟正黑體", sans-serif
```
## 確保裝置都能讀取字體：中英字體名稱皆設定
建議設定font-family時不只是要設定A的中文字體，也要設定A的英文字行名稱都要設定，這樣做是為了防止有些作業系統或裝置抓不到該中文字體名稱，兩者皆設定會比較保險。
舉例來說，微軟正黑體的英文名稱為：Microsoft JhengHei，那就可以像下面這樣設定
```
  font-family: Microsoft JhengHei, "微軟正黑體", sans-serif
```
## 在 Rails 5 裡引用特殊英文字體
如果設計師給你的字型是特殊的英文字體（也就是非內建字體），需包在專案內

> 舉例來說，今天設計師給我CenturyGothicBold字體，引用此字體步驟

1. 把字體包放到 assets/fonts資料架內
2. 用 Fontface Generator 產生各種支援的eot, woff, ttf 格式支援各系統
[Font-face 產生器](https://www.fontsquirrel.com/tools/webfont-generator)

上傳你要客製化使用的英文字體，Generator(產生器)便會自動生成各系統使用的字體檔


3. 在css上設定@font-face，若在rails引用路徑，可用 assets_path的helper
4. 針對個別需使用此字體的element（例如h1）或整體body設定 font-family: "CenturyGothicBold" 
```
@font-face {
  font-family: 'CenturyGothicBold';
  src: url('<%= asset_path("fonts/CenturyGothicBold.eot") %>');
  src: url('<%= asset_path("fonts/CenturyGothicBold.eot") %>') format('embedded-opentype'),
  		url('<%= asset_path("fonts/CenturyGothicBold.woff") %>') format('woff2'),
		url('<%= asset_path("fonts/CenturyGothicBold.woff") %>') format('woff'),
		url('<%= asset_path("fonts/CenturyGothicBold.ttf") %>') format('truetype')
		url('<%= asset_path("fonts/CenturyGothicBold.svg#CenturyGothicBold") %>') format('svg');
}
```

> 小知識：為什麼有這麼多種格式的字型？（woff2, woff, eot, ttf）

原因：有些瀏覽器只會讀取某種格式的字體檔，如果你想要把所有裝置瀏覽網頁都呈現統一的字體瀏覽的話，就得必須設這些擴充字體，讓某些不支援的瀏覽器或Android手機可以正常顯示你想要的字體

> 小知識：你知道字體有四大天王的格式嗎？

網頁上有四種字型格式：WOFF2、WOFF、EOT、TTF
有些瀏覽器只會讀取某種格式的字體檔，
- WOFF 2.0 支援的瀏覽器
- WOFF 給大部分瀏覽器
- EOT 給舊版 Android (4.4版以下) 的瀏覽器
- TTF 給舊版 IE ( IE 9 以下) 的瀏覽器

有興趣者可以參考這篇Stack發問：[Why should we include ttf, eot, woff, svg,… in a font-face
](https://stackoverflow.com/questions/11002820/why-should-we-include-ttf-eot-woff-svg-in-a-font-face)

## 三大作業系統 | 字體中英對照

### Mac OS

華文楷體：STKaiti
華文細明體：STSong
華文仿宋：STFangsong
儷黑 Pro：LiHei Pro Medium
儷宋 Pro：LiSong Pro Light
標楷體：BiauKai
蘋果儷中黑：Apple LiGothic Medium
蘋果儷細宋：Apple LiSung Light

### Windows

新細明體：PMingLiU
細明體：MingLiU
標楷體：DFKai-SB
黑體：SimHei
細明體：mingliu
新細明體：Nmingliu
仿宋：FangSong
楷體：KaiTi
仿宋_GB2312：FangSong_GB2312
標楷體：KaiTi_GB2312
微軟正黑體：Microsoft JhengHei
微軟雅黑體：Microsoft YaHei

### Linux
文泉驛正黑：WenQuanYi Zen Hei
全字庫正楷體：TW-Kai

## Reference | 參考資料
- [網頁中英文字型(font-family)跨平台設定最佳化](https://www.wfublog.com/2014/02/font-family-chinese-cross-platform.html)