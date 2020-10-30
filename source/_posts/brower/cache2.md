---
title: 浏览器的缓存机制
tags: 'brower'
categories: 'brower'
top_img: '/img/brower.jpeg'
---

浏览器缓存最主要的作用是减少网络传输的损耗以及降低服务器压力。
        
接下来我将通过以下几个部分来探讨浏览器缓存机制：
<ul>
<li>缓存位置</li>
<li>缓存策略</li>
</ul>

## 缓存位置

浏览器缓存位置分为四种，其优先级顺序如下：
<ol>
<li>Service Worker</li>
<li>Memory Cache</li>
<li>Disk Cache</li>
<li>Push Cache</li>
</ol>

当上述四个缓存位置中的缓存都没有命中时，则会向服务器发起请求。
### Service Worker

Service Worker 是一个注册在指定源和路径下的事件驱动 worker。它采用 JavaScript 控制关联的页面或者网站，拦截并修改访问和资源请求，细粒度地缓存资源。

我们可以通过谷歌开发者工具中的 Application -> Service Workers 查看当前缓存的资源。
### Memory Cache

Memory Cache 即内存中的缓存，其特点是容量小、读取高效、持续性短，会随着进程的释放而释放。

所以，在内存使用率低、缓存小尺寸资源时，会以 Memory Cache 为优先，否则使用 Disk Cache。
### Disk Cache

Disk Cache 即磁盘中的缓存，其特点是容量大、读取缓慢、持续性长，任何资源都能存储到磁盘中。

所以，在内存使用率高、缓存大尺寸资源时，会以 Disk Cache 为优先。
### Push Cache

Push Cache 是 HTTP 2.0 中的内容，其缓存时间也很短暂，只在会话（Session）中存在，一旦会话结束就被释放。

## 缓存策略

浏览器每次在向服务器发起 HTTP 请求获得资源后，可能会根据不同情况（可能是代码控制如 Service Worker、Push Cache，也可能是根据 HTTP Header 的缓存标识字段）将资源缓存起来。

浏览器缓存策略分为强制缓存和协商缓存，其是通过设置 HTTP Header 来实现的。
### 强制缓存

当浏览器发起 HTTP 请求时，会依次查找上述缓存位置中是否存在缓存资源并通过缓存标识字段 Expires 或 Cache-Control 来验证缓存资源是否过期。

Expires 是服务器端在响应请求时用来规定资源的失效时间。

Cache-Control 是服务器端在响应请求时用来规定资源是否需要被浏览器缓存以及缓存的有效时间等。

<figure></figure>



<a href="https://juejin.im/post/5c749f6851882562934ca96e" target="_blank">浏览器缓存机制</a>



<a href="https://mp.weixin.qq.com/s?__biz=MjM5MTA1MjAxMQ==&amp;mid=2651226347&amp;idx=1&amp;sn=6dbccc54406f0b075671884b738b1e88&amp;chksm=bd49596f8a3ed079f79cda4b90ac3cb3b1dbdb5bfb8aade962a16a323563bf26a0c75b0a5d7b&amp;scene=21#wechat_redirect" target="_blank">浏览器缓存机制剖析</a>

<a href="https://mp.weixin.qq.com/s?__biz=MjM5MTA1MjAxMQ==&amp;mid=207907457&amp;idx=1&amp;sn=cf0b3d1e630c977a839fdc8ee7c99904&amp;scene=21#wechat_redirect" target="_blank">构建高性能WEB之HTTP首部优化</a>

<a href="https://mp.weixin.qq.com/s?__biz=MjM5MTA1MjAxMQ==&amp;mid=2651226262&amp;idx=1&amp;sn=2128db200b88479face67ed8e095757c&amp;chksm=bd4959128a3ed0041b43a5683c75c4b88c7d35fac909a59c14b4e9fc11e8d408680b171d2706&amp;scene=21#wechat_redirect" target="_blank">浏览器的缓存机制小结</a>