---
title: 视频播放
tags: 'video'
categories: 'web'
top_img: '/img/html.jpg'
---
随着流量时代的到来和硬件技术的提升，越来越多的网站希望能在PC端或移动端播放自己的视频，而 video的兼容性的逐渐完善，使得开发者更愿意使用它来实现视频播放场景。


[视频播放--踩坑小计](https://juejin.im/post/5b189712f265da6e235488c1\)
****
## 场景一：自动播放

> autoPlay : boolean

指定后，视频会马上自动开始播放，不会停下来等着数据载入结束。

视频自动播放可以在页面打开且资源加载足够的情况下让视频自动播放，减少一次用户点击的交互，同时可以应用在动效背景、H5仿视频通话的功能。不过由于各种原因，自动播放无论在PC端还是移动端都有不同程度的限制。[看下一篇](www.webq.top/video-auto)

* 移动端 IOS

早期必须要有用户手势（user gesture）video标签才可以播放；
从版本10开始修改了video的规则，[苹果放宽了inline和autoplay](https://link.juejin.im?target=https%3A%2F%2Fwebkit.org%2Fblog%2F6784%2Fnew-video-policies-for-ios%2F)，策略如下（仅适用于Safari浏览器）：

video  
> elements will be allowed to autoplay  without a user gesture if their source media contains no audio tracks.(无音频源的 video 元素 允许自动播放)

video muted  
> elements will also be allowed to autoplay without a user gesture.

禁音的 video 元素允许自动播放

> If a 'video'  element gains an audio track or becomes un-muted without a user gesture, playback will pause.

如果 video 元素在没有用户手势下有了音频源或者变成非禁音，会暂停播放

video autoplay
>  elements will only begin playing when visible on-screen such as when they are scrolled into the viewport, made visible through CSS, and inserted into the DOM.

video 元素屏幕可见才开始播放

video autoplay
>  elements will pause if they become non-visible, such as by being scrolled out of the viewport.

video元素不可见后停止播放

* 安卓

早期 同样需要用户手势才可以播放；
安卓的 chrome 53 后[放宽了自动播放策略](https://link.juejin.im?target=https%3A%2F%2Fdevelopers.google.com%2Fweb%2Fupdates%2F2016%2F07%2Fautoplay)，策略不同于IOS的Safari，需要同时对 video 设置 autoplay  和 muted （是否禁音），才允许自动播放；

安卓的 FireFox 和 UC 浏览器 支持任何情况下的自动播放；

安卓的其他浏览器暂时不清楚情况；

* PC端

早期是支持自动播放，但近来 Safari、Chrome陆续修改了自动播放的策略……

Safari 浏览器
Safari 10  后带音频的视频和音频默认禁止自动播放，[更多信息可以参考这篇文章；](https://webkit.org/blog/7734/auto-play-policy-changes-for-macos)



Chrome 浏览器

禁音的视频依旧可以播放，带声音的视频会根据媒体参与指数 来决定能否自动播放，那什么是媒体参与指数？官方给了解释和相关的维度：

> MEI 是一个评估用户对于当前站点的媒体参与程度的指数，它取决于下面几个维度:

- 用户在媒体上停留时间超过了 7秒以上
- 音频必须是展示出来，并且没有静音
- 与 video 之间有过交互
- 媒体的尺寸不小于 200x140.

***检测能否自动播放***

好在无论是 Safari 还是 Chrome，在限制了自动播放的同时，提供了检测视频是否能自动播放的机制，以便于开发者在发现无法自动播放时有备选方案：

```javascript
var promise = document.querySelector('video').play();

if (promise !== undefined) {
        promise.catch(error => {
        // Auto-play was prevented
        // Show a UI element to let the user manually start playback
    }).then(() => {
            // Auto-play started
    });
}
 ```
思考

***为什么早期禁止视频自动播放?***

because it can be disruptive, many users don't like it.
[(因为它是破坏性的、需要大量流量同时很多用户不喜欢它)](https://link.juejin.im?target=http%3A%2F%2Fux.stackexchange.com%2Fquestions%2F5252%2Fvideo-and-audio-autoplay-evidence-that-its-bad-practice)


***为什么又允许自动播放?***

* 有些开发者使用其他方式如 canvas、gif 等来实现视频自动播放的效果，但是性能上、流量消耗上都远不如视频播放；

* 现在流量便宜了、手机硬件越来越好了；

* 用户可以通过设置来禁止自动播放（开启省流量模式等）；

***为什么 IOS 下微信和钉钉可以自动播放带声音的视频？***

确实发现在微信经常能看到自动播放的H5，但是作者自己写的设置了 autoplay、playsInline 的视频播放样例，在微信上依旧无法自动播放，而在钉钉上却可以自动播放

| 系统-浏览器 | 带声音  | 不带声音 |
|  ------   | ----   | ----  |
| IOS 钉钉   |  支持  | 支持   |
| IOS Safari | 禁止   | 自动播放  | 
| IOS 微信  | 禁止     | 禁止    | 

通过查询资料，IOS WebAPP 开发都是基于 IOS 提供的浏览器内核进行开发的 ，所以在 WebAPP 的 webview 中可以修改自动播放的表现，钉钉明显是支持自动播放，微信则是禁止自动播放，但是提供了内置事件来支持自动播放：


微信下通过 WeixinJSBridgeReady 事件进行自动播放：

```javascript
document.addEventListener(
      'WeixinJSBridgeReady',
  function() {
        video.play();
  },
  false
);
```
****
## 场景二：全屏处理

在移动端浏览器，  video 在用户点击播放或者通过API video.play() 触发播放时，会强制以全屏置顶的形式进行播放，设计的初衷可能是因为全屏能提供更好的用户体验，但有时候开发者希望能自己控制是否全屏从而实现其他需求。

> playsinline 取消全屏

如果想实现不全屏播放，只需在video标签加个 playsinline   属性即可，这个属性在基于webkit内核的移动端浏览器 基本没问题，实在不行就再加个 webkit-playsinline  ：

```html
<video
    src={videoUrl}
    webkit-playsinline="true"
    playsinline="true"
  />
```
那么对于其他内核的浏览器要怎么处理呢？这个时候要了解下目前市场上存在的浏览器有哪些。

>playsinline 兼容性

首先要知道全球目前四个浏览器内核：

- 微软IE的Trident 

网景最初研发后卖给Mozilla基金会并演化成火狐的Gecko 

- KDE的开源内核Webkit 

- Opera的Presto

其中：

> Trident 在移动端主要为WP7系统内置浏览器

> Presto 在所有联网设备上都使用，移动终端上主要为 Opera Mobile、OperaMini、欧朋浏览器以及欧朋HD Beta版

> Webkit 内核的适用范围则较为广泛，Android原生浏览器、苹果的Safari、谷歌的Chrome(Android4.0使用)都是基于Webkit开源内核开发的。


而国内常见的PC浏览器如UC浏览器、QQ浏览器、百度手机浏览器、360安全浏览器、谷歌浏览器、搜狗手机浏览器、猎豹浏览器 以及移动端的UC、QQ、百度等手机浏览器 都是根据Webkit修改过来的内核，本质上我们可以认为市场上移动端用户使用的基本上都是webkit内核或者基于 webkit 内核做修改的浏览器，所以 playsinline 的兼容性非常好！
___
## 场景三：播放控制

video 元素有提供多个行为事件供开发者控制视频播放，兼容性比较好的有 onended  、 ontimeupdate、onplay、onplaying 等，有些事件在不同浏览器不同设备上的的表现情况并不一致，


>例如：ios 下监听'canplay'（是否已缓冲了足够的数据可以流畅播放）,当加载时是不会触发的，即使preload="auto" 也没用，但在 pc 的 Chrome 调试器下，是会在加载阶段就触发。ios 需要播放后才会触发。

***部分事件在不同系统、设备、浏览器下显示的特性不一致，使用的时候需谨慎。***

___

## 场景四：隐藏播放控件


### controls

>加上这个属性，Gecko 会提供用户控制，允许用户控制视频的播放，包括音量，跨帧，暂停/恢复播放。

>controls 属性规定浏览器应该为视频提供播放控件，反之则隐藏播放控件，那么开发者可以自定义自己的播放控件。隐藏播放控件在 PC 端和 IOS 移动端兼容性良好，而在安卓移动端并不支持隐藏控件 ，不过还是可以通过一些方法来实现。

___

黑科技法


比较黑科技的方法是放大视频，把控件条移到视野之外，从而达到隐藏的效果！其实就是让视频元素比父容器还大，这样底部的控制条就会在父容器外面，然后父容器设置为：overflow:hidden  实现隐藏播放控件的方法！
缺点是视频会被放大，需要提前留好空白供放大用。


微信浏览器

腾讯的android团队的x5内核团队放开了视频播放的限制，视频不一定调用它们那个备受诟病的视频播放器了，利用x5-video-player-type=\"h5\" 属性隐藏控件元素，同时视频不再置顶，允许其他元素浮动在顶层 。
