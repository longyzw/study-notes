# tailwindcss

[官方文档](https://tailwindcss.com/docs/installation)

当前对应版本：v3.x

对应项目：vite => vue

## 安装

```sh
yarn add tailwindcss@latest postcss@latest autoprefixer@latest -D
```

## 生成配置文件

```sh
npx tailwindcss init -p
```

## 创建`tailwind.css`文件

静态资源目录css下创建该文件

```css
/* base样式会覆盖掉element-plus部分样式，如按钮背景，此项目使用normalize.css替换默认样式设置 */
/* @tailwind base; */
@tailwind components;
@tailwind utilities;
```

## 引用

在`main.js`引用文件

```javascript
import './assets/css/tailwind.css'
```

## 项目中使用

HTML中

```HTML
<div class="flex w-[200px] h-full text-lg bg-white"></div>
```

CSS中

```CSS
#app {
    @apply flex items-center rounded-md;
}
```