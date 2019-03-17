---
title: Vue 元件之間的傳話筒 總整理
date: 2019-03-12 22:40:08
tags:
---
使用 Vue 建構網站，會越來越多元件，元件之間的溝通非常重要，常常需要傳遞資訊，讓彼此知道，讓資訊流通是一件非常重要的事！

1. Props 父傳子
    - Parent => Child Communication
2. $emit 子傳父
    - 自定義事件，Child => Parent Communication
3. Event Bus
    - 類似於 Angular 2 的 services 用法

### Props 父傳子
用 Props 來傳遞資料，主元件要在外圍寫要傳什麼值進去，像是下面的範例，父元件傳了name給子元件值是Hazel。

``` js
// 父元件 User.vue
<user-detail :myName="Hazel"></user-detail>
```
子元件要新增 props 屬性來接收資料，接收到的name可以直接使用在其他地方，像是下面範例，接收到從父元件傳來的name，並使用在`switchName()的Methods`裡。
``` js
// 子元件 UserDetail.vue
<template>
<p> User Name: {{ switchName() }} </p>
</template>

export default {
    props: ['myName'],
    methods: {
        switchName(){
            return this.myName.split('').reverse().join('')
        }
    }
}
```

#### 限制 Props 資料類型
Prop 還能夠驗證資料型態，比如說在子元件下我們可以這樣寫
``` js
// 驗證 Props
props: ['myName', 'age', 'book']
props: {
    'myName': String,
    'age': Number,
    'book': Object
}
```
當myName值傳來不是String格式便會報錯，會在 Console.log 提示開發者由於傳遞錯誤的型態。
我們可以用這種方式來驗證資料，以避免程式出錯。

### Emit 子傳父
剛剛介紹了 Prop：父元件傳遞資料給子元件，但當子元件想要傳遞資料給父元件，該如何傳遞？我們會用到 `emit` 客製化事件來做到這件事

```
$emit('事件名稱', 要傳遞的值)
```

以下程式碼做的處理
1. 從子元件接收到myName的值
2. 子元件內 reset myName 值
3. 回傳給父元件

``` js
// UserDetail.vue 子元件
export default {
    props: {
        myName: {
            type: String
        }
    },
    methods: {
        switchName(){
            return this.myName.split('').revverse().join('');
        },
        resetName(){
            this.myName = 'Hazel';
            this.$emit('nameWasReset', this.myName);
        }
    }
}
```
子元件用`nameWasReset`事件傳遞
要接收訊息的父元件也要設定，將子元件設定的事件名稱用`@`當前綴
`@nameWasReset="name = $event"`，並存在父元件的資料name內。

> ### 小教學：關於Vue的$event
> $event 為 Vue內的特殊用法，相同於 JavaScript的 event
> $event 為子元間傳遞出來的值。

``` js
// User.vue 父元件
<template>
<user-detail :myName="name" @nameWasReset="name =$event"></user-detail>
</template>

<script>
export default {
    data(){
        return {
            name: 'Hazel'
        }
    }
}
</script>
```

#### Vue 單向資料流
單向資料流定義：只能從一個方向來修改，可以由父元件傳遞資料給子元件，但子元件無法反過來直接改變父元件的資料，會引起報錯。也就是只能父元件影響子，子無法影響父。
> #### 總結一句話：單向資料流只能父傳子、子傳父。
> Unidirectional Data Flow from top to bottom.

如果 A 的子元件想傳遞給 B 的子元件，平行傳資料是可行的嗎？
在Vue內，由於單向資料流的關係，無法子傳給子，是無法平行傳資料。

### CallBack Function
透過Props傳遞 function 也能做到元件溝通
父元件利用 Props 傳遞 resetFn()，值帶入methods的resetName()方法給子元件。
而子元件則用Props接收。

``` js
// 父元件
<template>
<user-detail :myName="name" @nameWasReset="name =$event" :resetFn="resetName"></user-detail>
</template>

<script>
import UserDetail from './UserDetail.vue';
export default {
    data(){
        return {
            name: 'Hazel'
        }
    },
        resetName(){
            this.name = 'Hazel';
        }
    },
    components: {
        UserDetail: UserDetail,
    }
}
</script>
```

下列程式碼是子元件接受來自父元件的`resetFn()`，型態是Function。
當點擊按鈕時會呼叫`resetFn()`，而值則是從父元件傳遞來的。
``` js
// 子元件
<template>
    <button @click="resetFn()">Reset Function</button>
</template>

<script>
export default {
    props: {
        myName: {
            type: String
        },
        resetFn: Function,
    }
}
</script>
```

### 兄弟(Sibling)元件間的傳值
Communication between sibling components

假設我們今天有 A 與 B 的子元件，要如何實行 A 與 B 間的傳遞？
- 父元件：User
- A子元件：UserEdit
- B子元件：UserDetail

``` js
// 父元件 User 傳遞值
<template>
<user-edit :userAge="age" @ageWasEdited="age = $event"></user-edit>
<user-detail :userAge="age"><user-detail>

<p>User Age: {{userAge}}</p>
</template>


<script>
export default {
    data(){
        return {
            age: '25'
        }
    }
}
</script>
```

A 與 B 子元件皆接收 userAge 資料，並驗證 Number值
A元件用$emit：ageWasEdited傳回age值為30給父元件
B元件也會一併跟著更改。

``` js
// A 子元件：UserEdit
<template>

    <p>User Age: {{userAge}}</p>
    <button @click="editAge">Edit Age</button>
</template>

<script>
export default {
    props: {
        userAge: Number
    },
    methods: {
        editAge(){
            this.userAge = 30;
            this.$emit('ageWasEdited', this.userAge);
        }
    }
}
</script>
```
以下是B元件 UserDetail ，B元件作用為 接收父元件的 UserAge 值
``` js
// B 子元件：UserDetail
<template>
<p>User Age: {{userAge}}</p>
</template>
<script>
export default {
    props: {
        userAge: Number
    }
}
</script>
```
從上面範例可以看出，當我們需要平行傳遞兄弟間的元件資料，可以用以下做法。
1. 父用props傳遞給 A 子元件
2. A 子元件 子用 $emit 回傳父，改變父資料
3. 再由父傳給另一個 B 子元件

## Event Bus：溝通橋樑
- Event Bus 的定位是事件通知。
- Event Bus使用時機：有件事要被很多人知道，可以用Event Bus來做傳遞，當傳話筒的角色，也可以形容成溝通橋樑的概念。
- EventBus像元件一樣，擁有data, methods等屬性可以使用。
- Event Bus 透過 $emit 來發送資料、用 $on 來接收訊息。

下列的範例會示範 A子元件與B子元件的溝通，但不會傳值到父元件。

1. 在main.js 建立一個 Vue instance 叫 eventBus
2. 要使用event bus傳遞訊息的元件上，import event bus進來
3. 在元件的methods上使用 `eventBus.$emit` 傳遞值


步驟一：創建 eventBus 的 instance
注意：要把eventBus建立在new Vue上，否則會報錯。
``` js
// main.js
export const eventBus = new Vue();
new Vue({
    el: '#app',
    render: h => h(App)
})
```
步驟二：要使用傳遞資料的元件上，import eventBus
``` js
// userEdit 元件
<script>
import { eventBus } from '../main';
</script>
```

步驟三：用eventBus.$emit 傳遞值
eventBus 是一個 Vue instance，而 Vue instance 內建有 $emit 的methods可以用。
> `eventBus.$emit('事件名稱', 值)`

``` js
<script>
import { eventBus } from '../main';
export default {
    props: ['userAge],
    methods: {
        editAge(){
            this.userAge = 30;
            // this.$emit('ageWasEdited', this.userAge) 第二種方法：$emit傳值
            eventBus.$emit('ageWasEdited', this.userAge) // 第三種方法：eventBus傳值
        }
    }
}
</script>
```

B 子元件 要接收值，也需要import eventBus，而接收值要使用到$on
> `eventBus.$on('事件名稱', callback function)`
接收ageWasEdited事件，回傳的data用callback function，再針對data做一系列的處理。
``` js
// B子元件 UserDetail
<script>
import { eventBus } from '../main';
export default {
    props: {
        userAge: Number
    }
    created(){
        eventBus.$on('ageWasEdited', (age) => {
            console.log(age);
            this.userAge = age; // 將傳來的age存進userAge內
        });
    }
}
</script>
```

### EventBus 方法
eventBus 除了可以讓子元件平行溝通之外，也能建立methods方法，並讓元件們共用。

1. 建立 eventBus 的 Vue instance，建立 changeAge 的 methods
2. 在要使用的子元件上 import eventBus

main.js上建立eventBus，並設定changeAge方法，接收參數age
``` js
// main.js
export const eventBus = new Vue({
    methods: {
        changeAge(age){
            this.$emit('ageWasEdited', age)
        }
    }
})
```
子元件 UserEdit.vue 利用 eventBus 的 changeAge 方法，傳遞userAge資料
``` js
import { eventBus } from '../main';
export default {
    props: [userAge],
    methods: {
        editAge(){
            this.userAge = 30;
            eventBus.changeAge(this.userAge);
        }
    }
}
```
同樣的 eventBus 也像元件一樣也擁有 data的屬性可儲存，這可以讓我們自由運用。

### 結論
- props：父傳子
- emit：子傳父
- eventBus：使用上非常方便，但維護性不高，最好把握幾個原則
    1. 僅限於同層的兄弟元件通訊，父子通訊透過 Props / $emit 來進行溝通
    2. eventBus缺點是記憶體會殘留，有監聽的地方要記得去清除，像是beforeDestroy等。
    3. 專案規模很大，可以考慮用 vuex來管理共同狀態。

以上重整理 Vue 元件間如何傳遞資料，若有誤歡迎留言告訴我，謝謝。

### Reference
- [[Vue] 跟著 Vue 闖蕩前端世界 - 07 組件溝通 event bus](https://dotblogs.com.tw/wasichris/2017/03/05/181549)