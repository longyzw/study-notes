# Vite

## 路径别名（ts）

```typescript
// vite.ts

export default defineConfig({
    resolve: {
        alias: {
            "@": resolve(__dirname, "src"), // 设置 `@` 指向 `src` 目录
        },
    }
})
 
// tsconfig.json
{
    "compilerOptions": {
        "paths": {
          	"@/*": ["./src/*"]
        }
    }
}
```

## 配置多环境及打包

### 配置文件

默认全局读取`.env`文件，例如：

- .env

  ```sh
  # 接口请求地址
  VITE_API_URL = ''
  
  # 资源输出目录
  VITE_BUILD_OUTDIR = 'dist'
  ```

注意：以`VITE_`为前缀的变量才会暴露给vite处理的代码

- .env.product

  ```sh
  # 接口请求地址
  VITE_API_URL = 'https://prod.xxx.com/'
  
  # 资源输出目录
  VITE_BUILD_OUTDIR = 'dist-prod'
  ```

### 使用变量

启用对应的环境：

启动或打包时在命令后传递` --mode 环境名称` （环境名称 => product），默认开发环境不用配置

默认使用方式：

```java
import.meta.env.VITE_API_URL
```

`vite.config`使用方式

```javascript
import { defineConfig, loadEnv } from 'vite'

export default ({ mode }) => {
  const env=loadEnv(mode, process.cwd())
  return  defineConfig({
    build: {
      outDir: env.VITE_BUILD_OUTDIR
    }
  })
}
```

### 打包

配置`package.json`

```json
{
  "scripts": {
    "default": "vite build",
    "prod": "vite build --mode prod"
  },
}
```

一键打包配置：

安装插件`concurrently`并行运行多个命令

```sh
yarn add concurrently
```

配置并行执行命令

```json
{
  "scripts": {
    "dev": "vite",
    "build": "concurrently \"yarn default\" \"yarn prod\"",
    "default": "vite build",
    "prod": "vite build --mode prod"
  }
}
```

