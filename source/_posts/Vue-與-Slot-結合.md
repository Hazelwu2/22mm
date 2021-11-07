---
title: Vue 與 Slot 結合
date: 2020-02-04 16:16:28
tags:
- vue
---
# Vue 與 Slot 結合

## Slot
Slot是什麼？可以讓你插入HTML內容。會使用的時機通常是結構都相同，但是裡面的HTML需要更改，CSS表現方式可能有三四種，這時Slot就可以派上用場了。

## 用法
### 匿名插槽
匿名插槽就是slot不設定name屬性，通常用在屬性較單純，只需要插入一個地方處。他會抓父元件template裡的內容。

子元件
```
 <template>
   <div class="content">
     <header class="text">
       <slot>預設值</slot>
     </header>
   </div>
 </template>
```
父元件，在template中的內容都會被帶入slot上。如果slot是變數，那他可以帶HTML各種格式填入進去。

```
// 父元件
<template>
  <div class="container">
    <child>
      <template>内容</template>
    </child>

    // 插槽上寫v-slot="slotProps"，父元件就可以讀取到子元件的data屬性
    <child v-slot="slotProps">
      {{ slotProps.user }}
    </child>
  </div>
</template>
```

### 具名插槽
最近流行武漢肺炎，去藥局都需要實名購買，Slot也是有實名制的！Slot有一個叫`name`的屬性，根據name來認內容。就和我們要去買口罩，他是看健保卡認的，才會給你買口罩喔XD。

```
// 子元件
<template>
  <div class="content">
    <main>
      <slot name="main"></slot>
    </main>
    <footer>
      <slot name="footer"></slot>
    </footer>
  </div>
</template>
```
子元件做了`main`與`footer`的插槽，接著來看父元件怎麼做設定

```
// 父元件
<template>
  <div class="container">
    <child>
      <template v-slot:main>
        <a href="https://www.zcygov.cn" target="_blank">导航</a>
      </template>
      <template #footer>页脚（具名插槽的缩写#）</template>
      <template #footer>
        <!--  v-slot 重复定义同样的 name 后只会加载最后一个定义的插槽内容 -->
        v-slot只会添加在一个 template 上面
      </template>
    </child>
  </div>
</template>
```
v-slot 如果定義同樣的 name，只會加載最後一個定義的slot內容。可以用#來做縮小
`name="footer"` 等同於 `#footer`


### 作用域插槽
作用域插槽就是可以讓父元件讀取到子元件的data及method，平常不是使用slot情況，父元件要吃子元件的method或data，是用`$emit`或`eventBus`來傳值。接下來示範在slot情況下，要怎麼父傳給子


```
// 子元件
 <template>
   <div class="content">
     <!-- 作用域插槽 -->
     <footer class="text">
       <slot name="footer" :user="user" :testClick="testClick">
         {{user.name}}
       </slot>
     </footer>
   </div>
 </template>

 <script>
 export default {
   name: 'child',
   data () {
     return {
       user: {
         title: 'Hazel Wu 你知道的 Hello',
         name: 'Hazel'
       }
     };
   },
   methods:{
     testClick(){
         // 這是子元件的方法，但也可以給父元件一起使用喔
       alert('123');
     }
   }
 };
 </script>
```
接著是父元件的設定，我們會在父元件定義`v-slot:xxx="slotProps"`或是`#xxx="slotProps"`，總之怎麼樣都一定少不了`slotProps`，當然`slotProps`也可以重新命名，他就是用來讀取子元件的資料或方法

```
// 父元件
<template>
  <div class="container">
    <child>
      <template #footer="slotProps">
        {{slotProps.user.title}}
        <button @click="slotProps.testClick">點我</button>
      </template>
      <template #footer="{user,testClick}">
        {{user.title}}<br>
        解構插槽prop <br>
        <!-- 子組件的 testClick方法，在父元件也可以使用喔 -->
        <button @click="testClick">点我</button>
      </template>
    </child>
  </div>
</template>

<script>
import Child from './component/child.vue';
export default {
  name: 'demo',
  components: {
    Child
  },
};
</script>
```
我們剛剛在子元件定義了`user的data`、`testClick的方法`，你看，我們在父元件都用得上，很方便！

## 實戰：Menu
我們的Menu有點複雜，我希望他有兩種型態，第一種型態是直接跳連結、第二種型態是show wrapper。

第一種型態：有li
```
// 父元件：展開Menu，有li
<MenuItem :show="showMenu = true">
   <template #name>關於我們</template>
   <template #content>
     <li class="col d-flex justify-content-center align-items-end">
       <div class="menu-item__wrapper__content">Show Content</div>
     </li>
     <li class="col d-flex justify-content-center align-items-end">
       <div>
         還在等什麼？快來訂閱我！
       </div>
     </li>
     <li class="col d-flex justify-content-center align-items-end">
       <div class="menu-item__wrapper__content"></div>
     </li>
   </template>
</MenuItem>
```
你可以看到父元件我用了兩個插槽，分別是`#name`, `#content`，由於選單都是用CSS的變化操作，所以並沒有用到slotProps，也就是子元件的資料及方法。

第二種型態：直接跳新頁面
```
// 父元件：第二種型態，直接跳新頁面
<MenuItem :show="showMenu = false">
   <template #name>
     優惠活動
   </template>
 </MenuItem>

export default {
  data() {
	return { showMenu: true }
  }
}
```
接著來看看子元件

```
<template>
  <div class="menu-item">
    <div class="position-absolute">
      <span class="d-inline-block mr-2">
        <slot name="name"></slot>
      </span>
      <i class="fas fa-chevron-up"></i>
      <div class="menu-item__line"></div>
    </div>

      <div class="menu-item__wrapper" v-if="show">
        <ul class="row d-flex align-items-center justify-content-between h-100">
          <slot name="content"></slot>
        </ul>
      </div>
    </slot>
  </div>
</template>
<script>
  export default {
    name: 'MenuItem',
    props: ['show'],
  }
</script>
```

子元件除了設定要插在哪裏的`name`、`content`之外，在`menu-item__wrapper`內另外用`v-if`來傳值，切換第一種型態、第二種型態選單的關鍵，再用props去接收show的值。

## Reference
- [官方文件：插槽內容](https://cn.vuejs.org/v2/guide/components-slots.html#%E6%8F%92%E6%A7%BD%E5%86%85%E5%AE%B9)
- [让你的组件千变万化，Vue slot 剖玄析微](https://www.zoo.team/article/slot)