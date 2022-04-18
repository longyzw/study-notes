# Pinia状态管理

[TOC]

项目模板选择 vite => vue3+ts

## 安装

- NPM

```sh
npm install pinia
```

- Yarn

```sh
yarn add pinia
```

## 挂载到应用程序

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

## 创建store文件夹及对应文件

```typescript
// src/store/app.ts

import { defineStore } from 'pinia'

export const useAppStore = defineStore('app', {
    state: () => {
        return {
            count: 1,
            msg: 'Hello'
        }
    },
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

## 使用

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

### 解构显示

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



## 修改state

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

## 组件之外使用

修改仓库

```typescript
import { defineStore, createPinia } from 'pinia'

export const useAppStore = defineStore('app', {
    // ... do something
})

export function useAppStoreWithOut() {
    return useAppStore(createPinia())
}
```

创建测试文件`test.ts`，并且在main.ts中pinia挂载前引入

```typescript
// test.ts
import { useAppStoreWithOut } from '~/store/app'

const test = () => {
    const appStore = useAppStoreWithOut()
    console.log('111', appStore.count);
}

export default test
```

```typescript
// main.ts

import test from '~/test'

test()
```

## 数据持久化

### 安装插件

```
yarn add pinia-plugin-persist
```

### 