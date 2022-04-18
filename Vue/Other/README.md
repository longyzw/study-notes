# 其余配置

## 适配h5

### 1、安装插件

```
npm i lib-flexible postcss-px2rem
```

### 2、配置vue.config.js

```
css: {
    loaderOptions: {
        css: {},
        postcss: {
            plugins: [
                require('postcss-px2rem')({
                	remUnit: 37.5
                })
            ]
        }
    }
},
```

### 3、配置HTML

删除HTML文件的<meta name='viewport'>标签

### 4、引入插件

```
import 'lib-flexible'
```

## setup语法糖使用组件name

### 1、安装插件

```
npm i vite-plugin-vue-setup-extend -D
```

### 2、配置vue.config.js

```javascript
import VueSetupExtend from 'vite-plugin-vue-setup-extend'

plugins: [
    VueSetupExtend()
]
```

### 3、使用

```javascript
<script setup lant="ts" name="ComponentsName"></script>
```

