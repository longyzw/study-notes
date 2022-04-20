# Gitbook

## Gitbook的安装

```
npm install -g gitbook-cli
```

## Gitbook的使用

* 新建文件夹

* 初始化项目

  ```
  gitbook init
  ```

  创建完成后会多两个文件

  README.md（书籍的介绍在这个文件里）

  SUMMARY.md（书籍的目录结构在这里配置）

- 目录结构示例

```
* [Vue3](Vue3/README.md)
    * [使用](Vue3/Introduction.md)
    * [vue2升级](Vue3/upgrade.md)
* [Vuex](Vuex/README.md)  
    * [使用](Vuex/Introduction.md)
* [Vite](Vite/README.md)
    * [使用](Vite/Introduction.md)
```

## 生成电子书

```
gitbook serve --watch
```

启动后访问：[http://localhost:4000](http://localhost:4000)

#### 常见启动问题：

1、cb.apply is not a function

解决方法：根据报错提示找到对应路径下的 `polyfills.js`文件，搜索`statFix`，注释掉如下代码

```
// fs.stat = statFix(fs.stat)
// fs.fstat = statFix(fs.fstat)
// fs.lstat = statFix(fs.lstat)
```

修改完保存后，重新启动即可。

## 打包

```
gitbook build
```

## 常用插件配置

项目根目录创建`book.json`

安装插件依赖：`gitbook install`

```json
{
    "title": "longyzw",
    "author" : "longyzw",
    "description" : "描述",
    "language" : "zh-hans",
    "plugins": [],
}
```

- #### 内联目录

  `intopic-toc`

  ```json
  {
    "plugins": [
      "intopic-toc"
    ],
    "pluginsConfig": {
      "intopic-toc": {
        "label": "内联目录名称"
      }
    }
  }
  ```

- #### 回到顶部

  `back-to-top-button`

  ```json
  {
    "plugins": [
      "back-to-top-button"
    ]
  }
  ```

- #### 导航目录折叠

  `chapter-fold`

  ```json
  {
    "plugins": [
      "chapter-fold"
    ]
  }
  ```

- #### 复制代码

  `code`

  ```json
  {
    "plugins": [
      "code"
    ]
  }
  ```

- #### 高级搜索

  支持中英文，移除默认搜索

  `search-pro`

  ```json
  {
    "plugins": [
      "-lunr",
      "-search",
      "search-pro",
    ]
  }
  ```

- #### 阅读量计数

  `pageview-count`

  ```json
  {
    "plugins": [
      "pageview-count"
    ]
  }
  ```

- #### 修改标题栏图标

  `custom-favicon`

  ```json
  {
    "plugins": [
      "custom-favicon"
    ],
    "pluginsConfig": {
      "favicon": "images/favicon.ico"
    }
  }
  ```

- #### 页面添加页脚

  `tbfed-pagefooter`

  ```json
  {
    "plugins": [
      "tbfed-pagefooter"
    ],
    "pluginsConfig": {
      "tbfed-pagefooter": {
          "copyright": "<span>©2019-2022 <a href='https://beian.miit.gov.cn' target='_blank' style='color: #4183c4 !important;text-decoration: none !important;'>蜀ICP备17040726号</a></span>",
          "modify_label": "最后编辑时间：",
          "modify_format": "YYYY-MM-DD HH:mm:ss",
          "modify_copy": ""
      }
    }
  }
  ```

  