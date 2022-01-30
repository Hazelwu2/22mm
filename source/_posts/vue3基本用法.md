---
title: Vue3 基本用法
date: 2022-01-30 19:29:20
tags:
category:
- vue3
---
此篇是寫下關於 Vue3 Composition API 基本用法


## 定義資料
在 vue2 中定義資料總是在 `data()` 去宣告，而在 vue3 則不同，有 ref、reactive 的方式

### Reactive
- 宣告的值一定要是物件，不可是純值
- 需使用 const，不可用 let，否則會失去雙向綁定

### Ref
- Vue 自行定義的屬性
- 和 Reactive 相同，需使用 const，不可用 let，否則會失去雙向綁定
- 宣告的值不限定任何型別，可以是純值

在實戰中 ref 會比 reactive 好用，且也是比較不會出錯的方式
不管是 ref、reactive 都要用 const 做宣告，才不會失去雙向綁定

### Vue2 範例
傳統在 Vue2 方式宣告 num
``` js
<template>
  <div>
    {{ num }}
  </div>
</template>

<script>
export default {
  data: (() => {
    num: 1
  })
}
</script>
```

### Vue3 範例
在 Vue3 則是要另外引入 ref 或 reactive，全部都在 setup() 裡完成，並在 return 時返回曾宣告的資料
讀取資料或設定資料都要加上 value，例如：`num.value`、`person.value`

``` js
<template>
  <div>
    {{ num }}
    {{ person }}
  </div>
</template>

<script>
export default {
  setup() {
    const num = ref(1)
    console.log(num.value)
    num.value = 17

    const person = ref({
      name: 'Hazel'
    })

    return {
      num,
      person
    }
  }
}
</script>
```

## props 接收及傳遞
在 Vue2 中，我們通常會用 props 接收來自父元件的參數
``` js
// 父元件
<template>
  <children :text="text" />
</template>

<script>
  export default {
    text: '傳給子元件'
  }
</script>

// 子元件
<template>
  <div>
  子元件
  {{ text }}
  </div>
</template>

export default {
  props: {
    text: {
      type: String,
      required: true,
      default: ''
    }
  }
}
```
而在 Vue3，改為 setup寫法後，setup可接收 props 值、context 值

``` js
export default {
  setup(props) {
    console.log(props.text)
  }
}
```
唯一要注意的是，props值不可直接寫成 ES6 解構，如果要使用解構 prop，必須用 `toRefs`

``` js
import { toRefs } from 'vuex

setup(props) {
  const { text } = toRefs(props)
  console.log(text.value)
}
```

## 使用 emit
子元件呼叫父元件事件，都會使用到 `emit`

### Vue2 範例

``` js
<template>
  <div>
    子元件
    <button @click="emit('emitToParent', 123)">
      範例emit按鈕
    </button>
  </div>
</template>

<template>
  // 父元件
  <div>
    <children @emitToParent="emitToParent" />
  </div>
</template>

<script>
// 父元件
export default {
  components: {
    children
  },
  methods: {
    emitToParent(value) {
      console.log('父層接收到子層資料', value)
    }
  }
}
</script>
```
### Vue3 範例
在 setup 引入 context.emit，也可以用解構 `{ emit }`來使用 emit
setup 接收兩個參數，第一個參數是 `props`，第二個參數則是 `context`

子元件範例：
``` js
<template>
  <div>
    子元件
    <button @click="emitToParent">
      範例emit按鈕
    </button>
  </div>
</template>

<template>
  <div>
    父元件
    <children @emitToParent="getData" />
  </div>
</template>

<script>
// 子元件
export default {
  setup(props, { emit }) {
    console.log(emit)

    function emitToParent() {
      emit('emitToParent', '子元件 -> 父元件資料')
    }
  }
}
// 父元件
export default {
  components: { children },
  setup() {
    function getData(text) {
      console.log(text) // 子元件 -> 父元件資料
    }

    return {
      getData
    }
  }
}
</script>
```

## $ref 取得 DOM 物件
在 Vue2 中呼叫 DOM 物件用法 
- 設定：`<button ref="btn" />`
- 讀取：`this.$refs['btn']`
``` js
<template>
  <button ref="btn">示範按鈕</button>
</template>

export default {
  mounted() {
    console.log(this.$refs.btn)
  }
}
```
而在 Vue3 則是改為以下寫法
設定和 Vue2相同，一樣在 HTML 標籤上加入 ref="btn"
另外要引入 ref、onMounted，最後必須 `return btn`

- 設定：`<button ref="btn" />`
- 讀取：`btn.value`
``` js
<template>
  <button ref="btn">示範按鈕</button>
</template>
// 宣告 ref, onMounted
const { ref, onMounted } = Vue

export default {
  setup() {
    const btn = ref(null) // undefined

    onMounted(() => {
      console.log(btn.value)
    })

    return {
      btn
    }
  }
}
```

## Provider、Inject 方法
跨元件層級的資料傳遞 Provider
假設是在最外層，可以運用 Provider 方法直接將資料傳到最內層
在父層使用 provide 傳出去，在子層用 inject 接收 `inject: ['user']`

傳遞
```
provide('user', user)
```
接收
```
inject('user')
```

### Vue2 寫法

子層
``` js
<script>
const children = {
  inject: ['user'],
  template: `<div>
  {{ user.name }}
  {{ user.uuid }}
</div>`,

  created() {
    console.log(this.user)
    this.user.name = 'Hazel'
  }
}
</script>
```
最外層
``` js
<script>
export default {
  components: { children },
  data() {
    return {
      user: {
        name: 'Ellie',
        uuid: 1348560
      }
    }
  },
  provide() {
    // provide function 也必須用 return
    return {
      user: this.user
    }
  }
}
</script>
```

### Vue3 範例
children 子元件，引入 inject 來接收 user 參數
子元件使用，如果會改變 user 值時，會避免影響到父層，建議使用淺層或深層拷貝方式處理


``` js
const { inject } from Vue

export default {
  setup(props) {
    const person = inject('user')

    return {
      person
    }
  }
}
```

父元件引入 provide，傳遞 user 物件
``` js
const { ref, provide } = Vue

export default {
  components: { children },
  setup() {
    const user = ref({
      name: 'Ellie',
      uuid: 1348560
    })

    // 自定義名稱，要發送的資料內容
    provide('user', user)

    return {
      user
    }
  }
}
```

## Watch
引入 watch，在 setup 設定 watch function
``` js
const { watch } = Vue
export default {
  setup() {
    watch(監聽的變數, (newValue, oldValue) => {
      ...
    })
  }
}

```

深層監聽物件屬性，加上 `deep: true`
``` js
watch(() => watchObject.value.skill, (newVal, oldVal) => {
  console.log(newVal, oldVal)
})
watch(watchObject, (newVal, oldVal) => {
  console.log(newVal, oldVal)
}, { deep: true })
```
### 多值監聽
需要監聽多個變數，可以用陣列監聽。但如果在同一個 function 裡同時改變這些監聽來源，watch 只會執行一次
``` js
const firstName = ref('')
const lastName = ref('')

watch([firstName, lastName], (newValues, preValues) => {
  console.log(newValues, preValues)
})
// 若監聽是深度嵌套对象或数组中的屬性改變，要加上 deep
watch([firstName, lastName], (newValues, preValues) => {
    console.log(newValues, preValues)
  }, 
  { deep: true }
)

firstName.value = 'Hazel' // Logs: ['Hazel', ''] ['', '']
lastName.value = 'Wu' // Logs: ['Hazel', 'Wu'] ['Hazel', '']
```

``` js
const { ref, watch } = Vue
export default {
  setup(props) {

    // 文字
    const name = ref('Hazel')
  
    watch(name, (newValue, oldValue) => {
      console.log(`新：${newValue}，舊：${oldValue}`)
    })

    // 物件
    const watchObject = ref({
      name: 'Hazel',
      skill: [
        'js',
        'vue',
        'nodejs'
      ]
    })

    // 物件深層監聽，需要加上箭頭函示
    watch(() => watchObject.value.skill, (newValue, oldValue) => {
      console.log(`新：${newValue}，舊：${oldValue}`)
    })

    return {
      name,
      watchObject
    }
  }
}
```