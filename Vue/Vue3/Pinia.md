# Pinia状态管理

项目模板选择 vite => vue3+ts

[官方文档](https://pinia.vuejs.org/introduction.html)

## 一、pinia

### 1、安装

- NPM

```sh
npm install pinia
```

- Yarn

```sh
yarn add pinia
```

### 2、挂载到应用程序

```typescript
// src/main.ts

import { createApp } from 'vue'
import { createPinia } from 'pinia'
import App from './App.vue'

const store = createPinia()

const app = createApp(App)
app.use(store)
app.mount('#app')

```

### 3、创建store文件夹及对应文件

```typescript
// src/store/app.ts

import { defineStore } from 'pinia'

export const useAppStore = defineStore('app', {
    state: () => ({
        count: 1,
        msg: 'Hello'
    }),
    getters: {
        // 方式1
        // doubleCount: (state) => state.count * 2,
        // 方式2
        // doubleCount(state) {
        //     return state.count * 2
        // },
        // 方式3
        // doubleCount() {
        //     return this.count * 2
        // },
        // 方式4
        doubleCount(state) {
            const doubleCount = state.count * 2
            return doubleCount
        },
    },
    actions: {
        increment() {
            this.count++
        },
        async increment2() {
            // await something
            this.count++
        }
    }
})
```

### 4、使用

App.vue

```html
<script setup lang="ts">
import { useAppStore } from './store/app'

const appStore = useAppStore()
</script>

<template>
  <div>state => count:{{appStore.count}}</div>
  <div>getters => doubleCount:{{appStore.doubleCount}}</div>
</template>
```

### 5、解构显示

```html
<script setup lang="ts">
import { storeToRefs } from 'pinia'
import { useAppStore } from './store/app'

const appStore = useAppStore()
const { count, doubleCount } = storeToRefs(appStore)
</script>

<template>
  <div>state => count:{{appStore.count}}</div>
  <div>getters => doubleCount:{{appStore.doubleCount}}</div>
  <hr>
  <div>storeToRefs => count:{{count}}</div>
  <div>storeToRefs => doubleCount:{{doubleCount}}</div>
</template>
```

### 6、修改state

App.vue

```html
<script setup lang="ts">
import { useAppStore } from './store/app'

const appStore = useAppStore()

function handleClickPatch() {
  appStore.$patch({
    count: appStore.count + 1,
    msg: appStore.msg === 'Hello' ? 'World' : 'Hello'
  })
}
function handleClickPatchState() {
  appStore.$patch((state) => {
    state.count = state.count + 1
    state.msg = state.msg === 'Hello' ? 'World' : 'Hello'
  })
}
</script>

<template>
  <div>state => count:{{appStore.count}}</div>
  <div>getters => doubleCount:{{appStore.doubleCount}}</div>
  <div>msg:{{appStore.msg}}</div>
  <hr>
  <button @click="appStore.count++">修改方法1：直接修改值</button>
  <br>
  <button @click="handleClickPatch">修改方法2：$patch</button>
  <br>
  <button @click="handleClickPatchState">修改方法3：$patch(state)</button>
  <br>
  <button @click="appStore.increment()">修改方法4：actions</button>
</template>

```

### 7、组件之外使用

创建测试文件`test.ts`，并且在main.ts中pinia挂载后调用

```typescript
// main.ts
import { createPinia } from 'pinia'
import test from '~/test'

const store = createPinia()

const app = createApp(App)
app.use(store)
app.mount('#app')

test()
```

## 二、数据持久化

[官方文档](https://seb-l.github.io/pinia-plugin-persist/)

### 1、安装插件

```sh
yarn add pinia-plugin-persist
```

### 2、挂载到pinia

```typescript
// main.ts
import { createApp } from 'vue'
import App from './App.vue'
import { createPinia } from 'pinia'
import piniaPersist from 'pinia-plugin-persist'

const store = createPinia()
store.use(piniaPersist)

const app = createApp(App)
app.use(store)
app.mount('#app')
```

### 3、基础用法

整个状态库开启sessionStorage存储

```typescript
export const useAppStore = defineStore('app', {
    state: () => ({}),
    getters: {},
    actions: {},
    // 会以当前库的id为key进行存储
    persist: {
        enabled: true
    }
})
```

### 4、高级用法

- 指定存储的key和存储方式

```typescript
export const useAppStore = defineStore('app', {
    state: () => ({}),
    getters: {},
    actions: {},
    persist: {
        enabled: true,
        strategies: [
            {
              key: 'user', // 指定存储的key
              storage: localStorage, // 指定存储的方式
            },
        ],
    }
})
```

- 设置部分存储

```typescript
export const useAppStore = defineStore('app', {
    state: () => ({}),
    getters: {},
    actions: {},
    persist: {
        enabled: true,
        strategies: [
            { storage: sessionStorage, paths: ['count'] },
            { storage: localStorage, paths: ['msg'] },
        ],
    }
})
```

- 自定义存储

存储有效期的token之类

