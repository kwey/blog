---
title: video新版本 Chrome自动播放报错
tags: 'video'
categories: 'web'
top_img: '/img/html.jpg'
---
> 解决新版本 Chrome 提示 DOMException: The play() request was interrupted


## 解决新版本 Chrome 提示
DOMException: The play() request was interrupted
大概在一些新版本的浏览器中，我们是用 audio 或者 video 进行媒体资源播放的时候，可能会在控制台看到这个输出:
``` javascript
Uncaught (in promise) DOMException: The play() request was interrupted by a call to pause(). 
```

Or
``` javascript
Uncaught (in promise) DOMException: The play() request was interrupted by a new load request.  
```

## 如何触发？
``` javascript
<video id="video" preload="none" src="https://example.com/file.mp4"></video>

<script> 
  video.play(); // <-- This is asynchronous!
  video.pause();
</script> 

```

上面的代码会造成下面的异常错误:
``` javascript
Uncaught (in promise) DOMException: The play() request was interrupted by a call to pause(). 
```

上面的 video 的 设置了 preload = 'none' ，视频并没有提前加载，因此在触发 video.play() 并不能立即播放。
从 Chrome 50 的版本后 触发 video.play 会返回一个 Promise 对象 ，如果播放成功，则 Promise 会触发，然后 playing 事件同时也会触发。如果播放失败，则会触发 Promise 的 reject,参数会带有错误的信息。
现在触发的顺序：
* video.play() 异步的去加载 video 内容，
* video.pause() 中断视频的加载，因为视频并没有就绪
* video.play() 异步的 rejects
因为我们并没有处理 video.play 的 Promise ，然后一个错误的信息会暴露出来。
video.pause() 不光会打断一个视频的播放，而且会完全重置一个视频的播放的状态，包括 buffer 和 video.load这些
## 修复
``` html
<video id="video" preload="none" src="https://example.com/file.mp4"></video>

<script> 
  // Show loading animation.
  var playPromise = video.play();

  if (playPromise !== undefined) {
        playPromise.then(_ =>{
          // 这个时候可以安全的暂停
      video.pause();
    })
    .catch(error =>{
    
    });
  }
</script> 

```

如今现在 HTMLMediaElement.play() 支持返回 Promise 已经在 Chrome, Firefox, Opera, 以及 Safari Edge 实现了。