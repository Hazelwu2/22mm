---
title: '[Vue] Vxe-Table 保存滾動捲軸的位置'
date: 2021-10-12 10:16:16
tags:
- vue
- vxe-table
---
今天要做的需求是，使用者不管點擊上一頁或下一頁，或是點擊頁面上任何連結，再次返回到那一頁，會保留原本瀏覽的位置。也就是說一開始進入 A 頁滑到頁面最尾端，跳轉到 B 頁，再回到 A 頁，我們希望 A頁進入後是在最尾端的位置。

官方文件提供 scrollBehavior 函式來自動記載捲軸滾動的位置，但僅限於點擊瀏覽器按上一頁、下一頁才會有作用。點擊 router-link 並不會有任何作用，console 出來 scrollBehavior 會發現是 null 值。
``` js
scrollBehavior (to, from, savedPosition) {
  if (savedPosition) {
    // 若點擊 router-link，savedPosition 會是 null 值
    // 並不會有任何作用
    return savedPosition 
  } else {
    return { x: 0, y: 0 }
  }
}
```
為了彌補 scrollBehavior 少做的功能，我們必須再多做點事。

## 外部
將 router 掛載的物件加入 meta.scrollTop 以方便去紀錄每個分頁的 Y軸位置，並在 router.beforeEach 離開頁面前寫入，進入頁面後 (router.afterEach) 滾動到指定位置

``` js
import router from './router'

// 設定 scrollBehavior
new Router({
  mode: 'history', // require service support
  scrollBehavior(to, from, savedPosition) {
    const scrollPos = {
      top: to.meta.scrollTop,
      left: 0
    }

    return savedPosition || scrollPos
  },
  routes: constantRoutes
})

router.beforeEach(async(to, from, next) => {
  // 離開頁面後，紀錄捲軸高度 (Y) 位置
  from.meta.scrollTop = window.scrollY
  next()
})

router.afterEach((to, from) => {
  // 頁面移動到上次瀏覽的位置
  setTimeout(() => {
    window.scrollTo(0, to.meta.scrollTop)
  }, 100)
})
```
也可以改寫，將 left, top 位置一併紀錄
``` js
{
  path: '/',
  component: Layout,
  redirect: '/dashboard',
  children: [
    {
      name: 'Dashboard',
      path: 'dashboard',
      component: () => import('@/views/other/dashboard/index'),
      children: [],
      meta: {
        title: {
          en: 'dashboard',
          zh: '儀表板'
        },
        icon: 'dashboard',
        affix: true,
        scrollPos = {
          top: 0,
          left: 0
        }
      }
    }
  ]
},
router.beforeEach(async(to, from, next) => {
  // 離開頁面後，紀錄捲軸高度 (Y) 位置
  from.meta.scrollPos.top = window.scrollY
  from.meta.scrollPos.left = window.scrollX
  next()
})

router.afterEach((to, from) => {
  // 頁面移動到上次瀏覽的位置
  setTimeout(() => {
    window.scrollTo(to.meta.scrollPos.left, to.meta.scrollPos.top)
  }, 100)
})
```

## 元件內部
使用 vxe-table 套件，只要設定 max-height後，即可紀錄保存位置

- scrollTo 移動到指定位置
- getScroll 取得表格的滾動狀態，必須設定 max-height 虛擬滾動表格才會紀錄，物件有  { virtualX, virtualY, scrollTop, scrollLeft }


``` js

<button @change="scrollTo">移動到指定1500位置</button>

<vxe-table 
  ref="xTable"
  show-overflow
  highlight-hover-row
  max-height="800"
  :data="tableData"
>
  <vxe-table-column field="test" align="center" />
</vxe-table-column>

mounted() {
  console.log(this.$refs.xTable)
  console.log(this.$refs.xTable.$el)
  console.log(this.$refs.xTable.$el.offsetTop)
},
methods: {
  getScroll() {
    console.log(this.$refs.xTable.getScroll())
    /*
      { virtualX, virtualY, scrollTop, scrollLeft }
    */
  },

  // 指定移動到 Y 軸 1500px 位置
  scrollTo() {
    this.$refs.xTable.scrollTo(0, 1500)
  }
}
```

### Reference
- [使用keep-alive保存滚动条的位置](https://juejin.cn/post/6975851743573868551)
- [Vue Router: Restore Scroll Position with <router-link> and <transition>](https://medium.com/@aryan02420/vue-router-restore-scroll-position-with-router-link-and-transition-61396af48ba2)