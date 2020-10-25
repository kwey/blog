---
title: 音视频自动播放问题
tags: 'Javascript'
categories: 'web'
# top_img: '/img/404.jpg'
---
不同浏览器下 autoplay 的限制策略和方案的整理

___
### 一、Chrome 浏览器

2018 年 4 月份发布的 Chrome 66 正式关掉了声音自动播放

* 静音自动播放总是允许的。
* 在下列情况下允许使用声音自动播放：
* 用户已经与域进行了交互（点击，tap 等）。在桌面上，用户的***媒体参与指数阈值***(MEI)已被越过，这意味着用户以前播放带有声音的视频。在移动设备上，用户已将该网站添加到主屏幕。顶部框架可以将自动播放权限授予其 iframe 以允许自动播放声音。


MEI 是一个评估用户对于当前站点的媒体参与程度的指数，它取决于
-   用户在媒体上停留时间超过了 7 秒以上
-   音频必须是展示出来，并且没有静音
-   与 video 之间有过交互
-   媒体的尺寸不小于 200x140

> Chrome-应对方案

- 不要假设视频会播放，并且在视频不是真正播放时不要显示暂停按钮。
- 根据 promise 结果，判断当前媒体是否支持 autoplay

``` javascript
var promise = document.querySelector("video").play();
if (promise !== undefined) {
    promise
        .catch(error =&gt; {
            // Auto-play was prevented
            // Show a UI element to let the user manually start playback
        })
        .then(() =&gt; {
            // Auto-play started
        });
```

> 如果不支持，有 3 个选择，
 - 静音自动播放，
 - 或者选择弹出 dialog 引导用户产生一次交互，在 event 回调里调用 play()
 - 提高 Chrome 浏览器的 MEI 指数

___
## Safari 浏览器

Safari11 允许用户通过「此网站的设置」选项，让用户控制哪些网站可以自动播放音视频

 判断当前媒体是否支持 autoplay
>如果不支持，有 3 个选择
- 静音自动播放引导用户对浏览器设置为【允许自动播放】
- 弹出 dialog 引导用户产生一次交互
- 在 event 回调里调用 play()
___
## 三、Firefox

 Firefox 66 和 Firefox for Android 开始，Firefox 将默认阻止音频和视频的自动播放

Firefox 也支持静音自动播放，只需将 HTMLMediaElement 的“muted”属性设置为 true，即可启用自动播放功能










