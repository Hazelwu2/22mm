---
title: Vue 動態切換元件
date: 2020-02-04 11:15:18
tags:
- vue
---
# Vue 動態切換元件
根據API取得的資料，動態切換元件。公司需要做模板切換，使用者可以在後台選擇layout1、layout2、layout3，分別會載入不同的layout

我們將API取得的資料放在`sessionStorage.siteInfo`裡，
接著利用`:is`來做動態切換。

當是layout1時，便會切換layout-1的元件。
比較特別的是，使用:is作法不需要在components再宣告一次
```
<template>
  <div>
    <div :is="hotGame"></div>
  </div>
</template>

import layout1 from '../12.index/hot_layout/layout-1.vue'
import layout2 from '../12.index/hot_layout/layout-2.vue';
import layout3 from '../12.index/hot_layout/layout-3.vue';

<script>
export default {
  data() {
    return {
      hotGame: this.selectLayout()
    };
  },
  methods: {
    selectLayout() {
      let layout = JSON.parse(sessionStorage.getItem('siteInfo')).site_params.hot_layout
      switch (layout) {
        case 'layout1':
          return layout1;
        case 'layout2':
          return layout2;
        case 'layout3':
          return layout3;
      }
    }
  } 
}
</script>
```

## 動態載入
import 使用變數來動態載入模組
```
var { hot_layout } = this.$store.dispatch('index.siteInfo');
var { vendor_layout } = JSON.parse(sessionStorage.getItem('siteInfo'));
var HotGame = () => import(`@/views/12.index/hot_layout/${hot_layout}.vue`)
var VendorGame = () => import(`@/views/12.index/vendor_layout/${vendor_layout}.vue`)

var c = 'components';
export default {
  components: {
    Slider: () => import(`@/views/12.index/${c}/Slider.vue`),
  },
}
```
但是這個方法取得API總會遲一步，所以暫緩沒有使用此方法，在這邊做個紀錄。

### Reference
- [官方文件：異步組件](https://cn.vuejs.org/v2/guide/components-dynamic-async.html#%E5%BC%82%E6%AD%A5%E7%BB%84%E4%BB%B6)
- [Vue.js: 動態元件 Dynamic Components](https://cythilya.github.io/2017/10/22/vue-component-dynamic-components/)
- [IT 鐵人賽 - Component 魔術方法 Day 5](https://blog.hinablue.me/2019-ithome-ironman-day-5/)
- [VueJS - 關於 vue-router 外面的兩三事](https://blog.hinablue.me/vuejs-guan-yu-vue-router-wai-mian-de-liang-san-shi/)