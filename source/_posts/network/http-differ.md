---
title: HTTP1.0、HTTP1.1 和 HTTP2.0 的区别
tags: 'http'
categories: 'brower'
top_img: '/img/brower.jpeg'
---
一、HTTP的基本优化
       
        
影响一个 HTTP 网络请求的因素主要有两个：<strong>带宽和延迟。

<ul><li>
带宽：忽略。</li><li>
延迟：</li><ul><li>
浏览器阻塞（HOL blocking）：浏览器会因为一些原因阻塞请求。浏览器对于同一个域名，同时只能有 4 个连接（这个根据浏览器内核不同可能会有所差异），超过浏览器最大连接数限制，后续请求就会被阻塞。</li><li>

</li><li>
DNS 查询（DNS Lookup）：浏览器需要知道目标服务器的 IP 才能建立连接。将域名解析为 IP 的这个系统就是 DNS。这个通常可以利用DNS缓存结果来达到减少这个时间的目的。</li><li>

</li><li>
建立连接（Initial connection）：HTTP 是基于 TCP 协议的，浏览器最快也要在第三次握手时才能捎带 HTTP 请求报文，达到真正的建立连接，但是这些连接无法复用会导致每次请求都经历三次握手和慢启动。三次握手在高延迟的场景下影响较明显，慢启动则对文件类大请求影响较大。</li></ul></ul>


二、HTTP1.0和HTTP1.1的一些区别<li>
缓存处理：</li><li>
HTTP1.0中主要使用header里的If-Modified-Since,Expires来做为缓存判断的标准，</li><li>
HTTP1.1则引入了更多的缓存控制策略例如<h5>Last-Modified / If-Modified-Since</h5>
Etag / If-None-Match
</li><li><h5>Cache-Control</h5></li><li>

</li><li>

</li><li>
带宽优化及网络连接的使用：</li><li>
HTTP1.0中，存在一些浪费带宽的现象，例如客户端只是需要某个对象的一部分，而服务器却将整个对象送过来了，并且不支持断点续传功能，</li><li>
HTTP1.1则在请求头引入了range头域，它允许只请求资源的某个部分，即返回码是206（Partial Content），这样就方便了开发者自由的选择以便于充分利用带宽和连接。</li><li>

</li><li>
错误通知的管理：</li><li>
在HTTP1.1中新增了24个错误状态响应码，如409（Conflict）表示请求的资源与资源的当前状态发生冲突；410（Gone）表示服务器上的某个资源被永久性的删除。</li><li>

</li><li>
Host头处理</li><li>
在HTTP1.0中认为每台服务器都绑定一个唯一的IP地址，因此，请求消息中的URL并没有传递主机名（hostname）。但随着虚拟主机技术的发展，在一台物理服务器上可以存在多个虚拟主机（Multi-homed Web Servers），并且它们共享一个IP地址。</li><li>
HTTP1.1的请求消息和响应消息都应支持Host头域，且请求消息中如果没有Host头域会报告一个错误（400 Bad Request）。</li><li>

</li><li>
长连接</li><li>
HTTP 1.1支持长连接（PersistentConnection）和请求的流水线（Pipelining）处理，在一个TCP连接上可以传送多个HTTP请求和响应，减少了建立和关闭连接的消耗和延迟，在HTTP1.1中默认开启Connection： keep-alive，一定程度上弥补了HTTP1.0每次请求都要创建连接的缺点。</li><li>

</li>
三、SPDY：HTTP1.x的优化
2012年google如一声惊雷提出了SPDY的方案，优化了HTTP1.X的请求延迟，解决了HTTP1.X的安全性，具体如下：

<li>
降低延迟</li><li>
针对HTTP高延迟的问题，SPDY优雅的采取了多路复用（multiplexing）。多路复用通过多个请求stream共享一个tcp连接的方式，解决了HOL blocking的问题，降低了延迟同时提高了带宽的利用率。</li><li>

</li><li>
请求优先级（request prioritization）</li><li>
多路复用带来一个新的问题是，在连接共享的基础之上有可能会导致关键请求被阻塞。SPDY允许给每个request设置优先级，这样重要的请求就会优先得到响应。比如浏览器加载首页，首页的html内容应该优先展示，之后才是各种静态资源文件，脚本文件等加载，这样可以保证用户能第一时间看到网页内容。</li><li>

</li><li>
header压缩</li><li>
前面提到HTTP1.x的header很多时候都是重复多余的。选择合适的压缩算法可以减小包的大小和数量。</li><li>

</li><li>
基于HTTPS的加密协议传输，大大提高了传输数据的可靠性。</li><li>

</li><li>
服务端推送（server push）</li><li>
采用了SPDY的网页，例如我的网页有一个sytle.css的请求，在客户端收到sytle.css数据的同时，服务端会将sytle.js的文件推送给客户端，当客户端再次尝试获取sytle.js时就可以直接从缓存中获取到，不用再发请求了</li><li>

</li>
SPDY位于HTTP之下，TCP和SSL之上，这样可以轻松兼容老版本的HTTP协议(将HTTP1.x的内容封装成一种新的frame格式)，同时可以使用已有的SSL功能。


四、HTTP2.0
<a href="https://link.zhihu.com/?target=https://http2.akamai.com/demo" target="_blank">https://link.zhihu.com/?target=https://http2.akamai.com/demo</a>

是 Akamai 公司建立的一个官方的演示，用以说明 HTTP/2 相比于之前的 HTTP/1.1 在性能上的大幅度提升。 同时请求 379 张图片，从Load time 的对比可以看出 HTTP/2 在速度上的优势。




五、HTTP2.0：SPDY的升级版


HTTP2.0可以说是SPDY的升级版（其实原本也是基于SPDY设计的），但是，HTTP2.0 跟 SPDY 仍有不同的地方，如下：


HTTP2.0和SPDY的区别：

<li>
HTTP2.0 支持明文 HTTP 传输，而 SPDY 强制使用 HTTPS</li><li>
HTTP2.0 消息头的压缩算法采用<a href="http://http2.github.io/http2-spec/compression.html" target="_blank">HPACK</a>，而非 SPDY 采用的 <a href="http://zh.wikipedia.org/wiki/DEFLATE" target="_blank">DEFLATE</a></li><li>

</li>
六、HTTP2.0和HTTP1.X相比的新特性

<ul><li>
新的二进制格式（Binary Format）</li><li>
HTTP1.x的解析是基于文本。基于文本协议的格式解析存在天然缺陷，文本的表现形式有多样性，要做到健壮性考虑的场景必然很多，二进制则不同，只认0和1的组合。基于这种考虑HTTP2.0的协议解析决定采用二进制格式，实现方便且健壮。</li><li>

</li><li>
多路复用（MultiPlexing）</li><li>
即连接共享，即每一个request都是是用作连接共享机制的。一个request对应一个id，这样一个连接上可以有多个request，每个连接的request可以随机的混杂在一起，接收方可以根据request的 id将request再归属到各自不同的服务端请求里面。</li><li>

</li><li>
header压缩</li><li>
如上文中所言，对前面提到过HTTP1.x的header带有大量信息，而且每次都要重复发送，HTTP2.0使用encoder来减少需要传输的header大小，通讯双方各自cache一份header fields表，既避免了重复header的传输，又减小了需要传输的大小。</li><li>

</li><li>
服务端推送（server push），同SPDY一样，HTTP2.0也具有server push功能。</li><li>

</li></ul>
七、HTTP2.0的升级改造<ul><li>
HTTP2.0其实可以支持非HTTPS的，但是现在主流的浏览器像chrome，firefox表示还是只支持基于 TLS 部署的HTTP2.0协议，所以要想升级成HTTP2.0还是先升级HTTPS为好。</li><li>

</li><li>
当你的网站已经升级HTTPS之后，那么升级HTTP2.0就简单很多，如果你使用NGINX，只要在配置文件中启动相应的协议就可以了，可以参考<strong>NGINX白皮书，<a href="https://www.nginx.com/blog/nginx-1-9-5/" target="_blank">NGINX配置HTTP2.0官方指南</a> 。</li><li>

</li><li>
使用了HTTP2.0那么，原本的HTTP1.x怎么办，这个问题其实不用担心，HTTP2.0完全兼容HTTP1.x的语义，对于不支持HTTP2.0的浏览器，NGINX会自动向下兼容的。</li><li>

</li></ul>
八、附注
HTTP2.0的多路复用和HTTP1.X中的长连接复用有什么区别？

<ul><li>
HTTP/1.* 一次请求-响应，建立一个连接，用完关闭；每一个请求都要建立一个连接；</li><li>
HTTP/1.1 Pipeling解决方式为，若干个请求排队串行化单线程处理，后面的请求等待前面请求的返回才能获得执行机会，一旦有某请求超时等，后续请求只能被阻塞，毫无办法，也就是人们常说的线头阻塞；</li><li>

</li><li>
HTTP/2多个请求可同时在一个连接上并行执行。某个请求任务耗时严重，不会影响到其它连接的正常执行；</li><li>

</li></ul>
<a href="http://www.ruanyifeng.com/blog/2018/03/http2_server_push.html" target="_blank">服务器推送到底是什么？</a>

服务端推送能把客户端所需要的资源伴随着index.html一起发送到客户端，省去了客户端重复请求的步骤。正因为没有发起请求，建立连接等操作，所以静态资源通过服务端推送的方式可以极大地提升速度。


为什么需要头部压缩？
假定一个页面有100个资源需要加载（这个数量对于今天的Web而言还是挺保守的）, 而每一次请求都有1kb的消息头（这同样也并不少见，因为Cookie和引用等东西的存在）, 则至少需要多消耗100kb来获取这些消息头。HTTP2.0可以维护一个字典，差量更新HTTP头部，大大降低因头部传输产生的流量。具体参考：<a href="https://imququ.com/post/header-compression-in-http2.html" target="_blank">HTTP/2 头部压缩技术介绍</a>


HTTP2.0多路复用有多好？HTTP 性能优化的关键并不在于高带宽，而是低延迟。TCP 连接会随着时间进行自我「调谐」，起初会限制连接的最大速度，如果数据成功传输，会随着时间的推移提高传输的速度。这种调谐则被称为 TCP 慢启动。由于这种原因，让原本就具有突发性和短时性的 HTTP 连接变的十分低效。HTTP/2 通过让所有数据流共用同一个连接，可以更有效地使用 TCP 连接，让高带宽也能真正的服务于 HTTP 的性能提升。




参考文档：
<a href="https://www.nihaoshijie.com.cn/index.php/archives/698/" target="_blank">多路复用与keep-alive</a>

<a href="http://www.alloyteam.com/2016/07/httphttp2-0spdyhttps-reading-this-is-enough/" target="_blank">HTTP,HTTP2.0,SPDY,HTTPS你应该知道的一些事</a>

<a href="https://mp.weixin.qq.com/s?__biz=MjM5MDE0Mjc4MA==&amp;mid=2650995461&amp;idx=1&amp;sn=ff45463bbf862517761c17887ef3fd2d" target="_blank">腾讯HTTPS性能优化实践</a>

<a href="https://zh.wikipedia.org/wiki/%E5%82%B3%E8%BC%B8%E5%B1%A4%E5%AE%89%E5%85%A8%E6%80%A7%E5%8D%94%E5%AE%9A" target="_blank">传输层安全性协议</a> TLS/SSL

<a href="https://zh.wikipedia.org/wiki/HTTP%E4%B8%A5%E6%A0%BC%E4%BC%A0%E8%BE%93%E5%AE%89%E5%85%A8" target="_blank">HTTP严格传输安全</a> HSTS

<a href="http://www.alloyteam.com/2015/03/http2-0-di-qi-miao-ri-chang/" target="_blank">HTTP2.0的奇妙日常</a>