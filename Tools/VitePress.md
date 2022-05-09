# VitePress

[官网](https://vitepress.vuejs.org/)

## 一、初始化环境

- 创建项目目录

  ```sh
  mkdir vitepress
  ```

- 初始化包管理

  ```sh
  yarn init -y
  ```

- 安装VitePress

  ```sh
  yarn add vitepress -D
  ```

- 创建入口文件

  ```sh
  # 根目录创建 docs 文件夹
  mkdir docs
  # 创建/docs/index.md文件
  echo '# Hello VitePress' > docs/index.md
  ```

- 添加包脚本

  ```sh
  # package.json
  "scripts": {
      "dev": "vitepress dev docs",
      "build": "vitepress build docs",
      "serve": "vitepress serve docs"
  },
  ```

- 启动本地服务

  ```sh
  yarn dev
  ```

## 二、配置

- 创建配置文件

  ```sh
  # /docs/.vitepress/config.js
  
  # 基础配置
  module.exports = {
    lang: 'zh-CN',
    title: 'VitePress',
    description: 'note',
    vite: {
      server: {
        host: '0.0.0.0',
        port: 8080
      }
    }
  }
  ```

