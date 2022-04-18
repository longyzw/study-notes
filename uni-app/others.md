# 常规事项

## 1、多行超出显示省略号

```css
display: -webkit-box;
overflow: hidden;
text-overflow: ellipsis;
word-wrap: break-word;
white-space: normal !important;
-webkit-line-clamp: 2; /* 显示行数 */
-webkit-box-orient: vertical;
```

## 2、处理uni-app text自带行高

uniapp  text标签自带行高，解决方法如下：

- 在text标签外围套一层view标签，将line-height写在view中
- 设置父级元素 display:flex; flex-wrap:wrap;
- text标签增加display:block;

## 3、video标签在Android H5支持播放.m3u8 hls直播流

控制台会报错：

```
Uncaught (in promise) DOMException: The element has no supported sources.
```

通过hls.js来解决，在App.vue的onLaunch方法中增长加下列代码便可:

```javascript
// 下面的代码增长对Android H5播放m3u8支持
// #ifdef H5
if (uni.getSystemInfoSync().platform != 'ios') {
    var script = document.createElement("script");
    script.src = "https://cdn.jsdelivr.net/npm/hls.js@latest";
    script.onload = function() {
        let init = function(video) {
            if (!video.getAttribute('hls-inited')) {
                let hls = null;
                let load = function() {
                    let src = video.src;
                    if (!src || src.indexOf('.m3u8') < 0)
                        return
                    if (!hls) {
                        hls = new Hls();
                        hls.attachMedia(video);
                    }
                    hls.loadSource(src);
                }
                video.addEventListener("error", function() {
                    load();
                }, false);
                video.addEventListener("DOMNodeRemovedFromDocument",function(){
                    if(hls){
                        hls.destroy()
                    }
                }, false);
                video.setAttribute('hls-inited', 'ok');
            }
        }
        document.getElementsByTagName("video").forEach(init)
        document.body.addEventListener("DOMNodeInserted", function(e) {
            let ele = e.relatedNode;
            if (ele.tagName === 'VIDEO') {
                init(ele);
            }
            ele.getElementsByTagName("video").forEach(init)
        })
    }
    var s = document.getElementsByTagName("script")[0];
    s.parentNode.insertBefore(script, s);
}
// #endif
```