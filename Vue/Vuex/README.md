# 创建及使用

### 创建（单文件）

```javascript
import { createStore } from 'vuex'

export default createStore({
  state () {
    return {
      count: 0
    }
  },
  mutations: {
    increment (state) {
      state.count++
    }
  }
})
```

### 创建（多模块）

#### 业务结构

```
|-- store
	|-- index.js
	|-- modules
    	|-- module1.js
    	|-- module2.js
    	|-- module3.js
```

#### 示例

```javascript
/* module1.js */
const state = {
    type1: 'module1-1',
    type2: 'module1-2',
}

const mutations = {
    SET_TYPE1: (state, val) => {
        state.type1 = val
    }
}

export default {
    namespaced: true,
    state,
    mutations
}
```

```javascript
/* getters.js */
export default {
    type1_1: state => state.module1.type1,
    type1_2: state => state.module1.type2,
    type2_1: state => state.module2.type1,
    type2_2: state => state.module2.type2,
}
```

```javascript
/* index.js -- vite 方式 */
import { createStore } from 'vuex'
import getters from './getters'

// 获取modules目录下所有的js文件
const modulesFiles = import.meta.glob('./modules/*.js')

const modules = {}
// 遍历文件对象
for (const path in modulesFiles) {
    // 获取modules目录下每个文件的文件名
    const moduleName = path.replace(/^\.\/modules\/(.*)\.\w+$/, '$1')
    // 获取每个文件的内容
    const resource = await modulesFiles[path]()
    // 重新把值赋值给modules对象
    modules[moduleName] = resource.default
}

export default createStore({
    getters,
    modules
})
```

```javascript
/* index.js -- webpack 方式 */
import { createStore } from 'vuex'
import getters from './getters'
// 获取modules目录下所有的js文件
const modulesFiles = require.context('./modules', true, /\.js$/)

const modules = modulesFiles.keys().reduce((modules, path) => {
  // 获取modules目录下每个文件的文件名
  const moduleName = path.replace(/^\.\/(.*)\.\w+$/, '$1')
  // 获取每个文件的内容
  const value = modulesFiles(modulePath)
  // 重新把值赋值给modules对象
  modules[moduleName] = value.default
  return modules
}, {})

export default createStore({
    getters,
    modules
})
```



### 挂载到vue实例

```javascript
import { createApp } from 'vue'
import store from '~/store'

const app = createApp()
app.use(store)
```

### 使用

注意：Vue3取消了this指向根实例

```javascript
<template>
  <button type="button" @click="addNum">count is: {{ state.count }}</button>
  // 单文件时
  <div>{{store.state.count}}</div>
  // 多文件时
  <div>{{store.state.module1.type1}}</div>
  
</template>

import { reactive } from 'vue'
import { useStore } from 'vuex'

export default {
  setup () {
    const state = reactive({ count: 0 })
    const store = useStore()

    function addNum() {
      state.count++
      // 单文件时
      store.commit('increment')
      // 多文件时
      store.commit('module1/SET_TYPE1', state.count)
    }
  }
}
```

其他方法的使用同`vuex@3`