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
    title: '🐷',
    description: 'note',
    // 主题配置
    themeConfig: {
      // 获取每个文件最后一次 git 提交的 UNIX 时间戳(ms)，同时它将以合适的日期格式显示在每一页的底部
      lastUpdated: true, // string | boolean
      // 启动页面丝滑滚动
      smoothScroll: true,
      // 导航栏配置
      nav: [
        { text: '介绍', link: '/introduce' },
        { text: '个人网站', link: 'http://www.ylsong.com' },
        { text: 'Github', link: 'https://github.com/admin-common-template' }
      ],
      // 侧边栏
      sidebar: {
        '/': getSidebar(),
      }
    }
  }
  
  function getSidebar() {
    return [
      {
        text: "介绍",
        link: "/introduce"
      },
      {
        text: "Admin后台系统",
        children: [
          {
            text: "环境搭建",
            children: [
              {
                text: "初始化项目",
                link: "/admin/environment/setup1"
              },
            ]
          }
        ],
      },
      {
        text: "服务端",
        link: "/server/"
      },
      {
        text: "数据库",
        link: "/sql/"
      }
    ]
  }
  ```
  
  ```javascript
  
  import { defineConfig } from 'vite'
  
  export default defineConfig({
      server: {
          host: '0.0.0.0',
          port: 8080
      },
  })
  ```
  
  