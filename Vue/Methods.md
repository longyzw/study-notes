# 常用方法/组件封装

# 页面刷新

### vue2

### vue3

使用`provide / inject`

路由控制显示组件

```HTML
<template>
    <router-view v-slot="{ Component }">
        <keep-alive :include="cacheList">
            <component v-if="isRouterAlive" :is="Component" />
        </keep-alive>
    </router-view>
</template>

<script setup>
import { ref, provide } from 'vue'

const isRouterAlive = ref(true)
// 局部组件刷新
const reload = () => {
  isRouterAlive.value = false
  nextTick(() => {
    isRouterAlive.value = true
  })
}
provide('reload', reload)
</script>
```

执行刷新组件

```HTML
<template>
    <span @click="refresh">刷新</span>
</template>

<script setup>
import { inject } from 'vue'

const refresh = inject('reload')
</script>
```