# 通用npm插件

## 并行运行命令

[npm仓库地址](https://www.npmjs.com/package/concurrently)

### 安装

```sh
yarn add concurrently
```

### 使用

```
"start": "concurrently \"command1 arg\" \"command2 arg\""
```

## 浏览器指纹

[npm仓库地址](https://www.npmjs.com/package/@fingerprintjs/fingerprintjs)

### 安装

```sh
yarn add @fingerprintjs/fingerprintjs
```

### 使用

```javascript
import FingerprintJS from '@fingerprintjs/fingerprintjs'

async function getFP() {
  const fp = await (await FingerprintJS.load()).get()
  return fp.visitorId
}
await getFP() // => 7a9e26a78bf05fb9aa1016b7f0a56457
```

## 切换全屏

[npm仓库地址](https://www.npmjs.com/package/screenfull)

### 安装

```sh
yarn add screenfull
```

### 使用

```javascript
import screenfull from 'screenfull'

// 监听全屏变化
screenfull.on('change', () => {
  console.log(screenfull.isFullscreen) // => 全屏状态
})

// 切换全屏状态
function toggleScreen() {
  screenfull.toggle()
}
```