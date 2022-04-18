# 常用css方法

## 文本溢出隐藏

```scss
// 文本溢出隐藏，默认隐藏单行
@mixin text-line($num: 1) {
    // 单行隐藏
    @if $num == 1 {
        white-space: nowrap;
    }
    text-overflow: -o-ellipsis-lastline;
    overflow: hidden;
    text-overflow: ellipsis;
    // 多行隐藏
    @if $num > 1 {
        display: -webkit-box;
        word-break: break-all;
        -webkit-line-clamp: $num;
        -webkit-box-orient: vertical;
    }
}
```

使用：

隐藏单行：@include text-line();

隐藏多行：@include text-line(n);

## 隐藏指定方向上的滚动条

```scss
// 隐藏滚动条，默认y
@mixin hideScroll($xy: y) {
    overflow: hidden;
    overflow-#{$xy}: auto;
    scrollbar-width: none;
    -ms-overflow-style: none;
    &::-webkit-scrollbar {
        display: none;
    }
}
```

## 网站初始化加载效果

css代码：

```css
html,body,#_app{display:flex;justify-content:center;align-items:center;height:100vh;margin:0;padding:0;overflow:hidden;}.chromeframe{margin:.2em 0;color:#4ea3fb;padding:.2em 0}#loader-wrapper{position:fixed;top:0;left:0;width:100%;height:100%;z-index:999999}#loader{display:block;position:relative;left:50%;top:50%;width:150px;height:150px;margin:-75px 0 0 -75px;border-radius:50%;border:3px solid transparent;border-top-color:#4ea3fb;-webkit-animation:spin 2s linear infinite;-ms-animation:spin 2s linear infinite;-moz-animation:spin 2s linear infinite;-o-animation:spin 2s linear infinite;animation:spin 2s linear infinite;z-index:1001}#loader:before{content:"";position:absolute;top:5px;left:5px;right:5px;bottom:5px;border-radius:50%;border:3px solid transparent;border-top-color:#4ea3fb;-webkit-animation:spin 3s linear infinite;-moz-animation:spin 3s linear infinite;-o-animation:spin 3s linear infinite;-ms-animation:spin 3s linear infinite;animation:spin 3s linear infinite}#loader:after{content:"";position:absolute;top:15px;left:15px;right:15px;bottom:15px;border-radius:50%;border:3px solid transparent;border-top-color:#4ea3fb;-moz-animation:spin 1.5s linear infinite;-o-animation:spin 1.5s linear infinite;-ms-animation:spin 1.5s linear infinite;-webkit-animation:spin 1.5s linear infinite;animation:spin 1.5s linear infinite}@-webkit-keyframes spin{0%{-webkit-transform:rotate(0deg);-ms-transform:rotate(0deg);transform:rotate(0deg)}100%{-webkit-transform:rotate(360deg);-ms-transform:rotate(360deg);transform:rotate(360deg)}}@keyframes spin{0%{-webkit-transform:rotate(0deg);-ms-transform:rotate(0deg);transform:rotate(0deg)}100%{-webkit-transform:rotate(360deg);-ms-transform:rotate(360deg);transform:rotate(360deg)}}#loader-wrapper .loader-section{position:fixed;top:0;width:51%;height:100%;background:#241e1e;z-index:1000;-webkit-transform:translateX(0);-ms-transform:translateX(0);transform:translateX(0)}#loader-wrapper .loader-section.section-left{left:0}#loader-wrapper .loader-section.section-right{right:0}
```

使用：

在项目的首页引用css代码，

html添加：

```html
<div id="_app">
    <div id="loader-wrapper">
        <div id="loader"></div>
        <div class="loader-section section-left"></div>
        <div class="loader-section section-right"></div>
    </div>
</div>
```

移除loading效果：

```javascript
const _app = document.getElementById('_app')
if (_app) {
    _app.remove()
}
```

## 表单默认自动填充样式

```css
/* 延迟自动填充样式覆盖时间 */
input:-webkit-autofill,
input:-webkit-autofill:hover,
input:-webkit-autofill:focus,
input:-webkit-autofill:active {
    -webkit-transition-delay: 99999s;
    -webkit-transition: color 99999s ease-out, background-color 99999s ease-out;
}
```

