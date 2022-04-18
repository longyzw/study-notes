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

