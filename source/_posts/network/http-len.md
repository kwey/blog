---
title: http连接优化与浏览器允许的并发请求资源数
tags: 'http'
categories: 'brower'
top_img: '/img/brower.jpeg'
---

> 前端技术的逐渐成熟，还衍生了domain hash, cookie free, css sprites, js/css combine, max expires time, loading images on demand等等技术。这些技术的出现和大量使用都和并发资源数有关


<li><a href="https://www.cnblogs.com/hubing/p/4922508.html" target="_blank">原文地址</a></li><li></li><li>1、按照普通设计，当网站cookie信息有1 KB、网站首页共150个资源时，用户在请求过程中需要发送150 KB的cookie信息，在512 Kbps的常见上行带宽下，需要长达3秒左右才能全部发送完毕。 尽管这个过程可以和页面下载不同资源的时间并发，但毕竟对速度造成了影响。 而且这些信息在js/css/images/flash等静态资源上，几乎是没有任何必要的。 解决方案是启用和主站不同的域名来放置静态资源，也就是cookie free。</li><li>

</li><li>2、将css放置在页面最上方应该是很自然的习惯，但第一个css内引入的图片下载是有可能堵塞后续的其他js的下载的。而在目前普遍过百的整页请求数的前提下，浏览器提供的仅仅数个并发，对于进行了良好优化甚至是前面有CDN的系统而言，是极大的性能瓶颈。 这也就衍生了domain hash技术来使用多个域名加大并发量（因为浏览器是基于domain的并发控制，而不是page），不过过多的散布会导致DNS解析上付出额外的代价，所以一般也是控制在2-4之间。 这里常见的一个性能小坑是没有机制去确保URL的哈希一致性（即同一个静态资源应该被哈希到同一个域名下），而导致资源被多次下载。</li><li>
</li><li>再怎么提速，页面上过百的总资源数也仍然是很可观的，如果能将其中一些很多页面都用到的元素如常用元素如按钮、导航、Tab等的背景图，指示图标等等合并为一张大图，并利用css background的定位来使多个样式引用同一张图片，那也就可以大大的减少总请求数了，这就是css sprites的由来。</li><li>
</li><li>3、全站的js/css原本并不多，其合并技术的产生却是有着和图片不同的考虑。 由于cs/js通常可能对dom布局甚至是内容造成影响，在浏览器解析上，不连贯的载入是会造成多次重新渲染的。因此，在网站变大需要保持模块化来提高可维护性的前提下，js/css combine也就自然衍生了，同时也是minify、compress等对内容进行多余空格、空行、注释的整理和压缩的技术出现的原因。</li><li>
</li><li>4、随着cookie free和domain hash的引入，网站整体的打开速度将会大大的上一个台阶。 这时我们通常看到的问题是大量的请求由于全站公有header/footer/nav等关系，其对应文件早已在本地缓存里存在了，但为了确保这个内容没有发生修改，浏览器还是需要请求一次服务器，拿到一个304 Not Modified才能放心。 一些比较大型的网站在建立了比较规范的发布制度后，会将大部分静态资源的有效期设置为最长，也就是Cache-Control max-age为10年。 这样设置后，浏览器就再也不会在有缓存的前提下去确认文件是否有修改了。 超长的有效期可以让用户在访问曾访问过的网站或网页时，获得最佳的体验。 带来的复杂性则体现在每次对静态资源进行更新时，必须发布为不同的URL来确保用户重新加载变动的资源。</li><li>
</li><li>5、即使是这样做完，仍然还存在着一个很大的优化空间，那就是很多页面浏览量很大，但其实用户直接很大比例直接就跳走了，第一屏以下的内容用户根本就不感兴趣。 对于超大流量的网站如淘宝、新浪等，这个问题尤其重要。 这个时候一般是通过将图片的src标签设置为一个loading或空白的样式，在用户翻页将图片放入可见区或即将放入可见区时再去载入。 不过这个优化其实和并发资源数的关系就比较小了，只是对一些散布不合理，或第一页底部的资源会有一定的帮助。 主要意图还是降低带宽费用。</li>

## 配置nginx实现通过cookie-free域名发送静态资源

## 使用 cookie-free 域名的好处


当浏览器加载 HTML 文件中引用的静态资源 —— 如图片、外部 CSS、外部 JS 等 —— 时，若该资源所属域与当前页面相同，则会在 HTTP 头请求中加载当前域的 cookie 信息。


你的网站为 http://www.whatever.com，启用了 Google Analytics 或百度统计或任意第三方统计代码。用户访问你的网站首页，首页 html 代码引用了 10 个图片文件，图片地址是 `http://www.whatever.com/images/[0-9].jpg` （此处有正则）。


因为 Google Analytics 在 http://www.whatever.com 这个域下设置了 cookie，浏览器在加载这些图片时，会把 Google Analytics 设置的 cookie 放在头信息里发过去。


本来一个 cookie 也没多大，顶多 1KB，但是如果你要加载 50 个图片（或其它静态文件），这样发送的 cookie 总量就多达 50KB 了。对于静态资源来说，发送这些 cookie 完全没有意义，所以我们不想让浏览器请求这些静态资源时发送 cookie。


下图是苹果教程网未设置 cookie-free 域名前请求某静态资源时 HTTP 请求头中包含 cookie 内容的惨状

## 配置nginx实现cookie-free域名


其实单独把 cookie-free 域名这个事儿单独拎出来说是一件很伤感的事情，因为有钱淫一般都会同时选择 VIP 套餐：CDN。使用 CDN，将静态资源分发在多台服务器上，用户在请求资源时智能判断哪个节点速度最快并返回，同时再启用一个 cookie-free 域名用来指向静态资源。


对于没有能力搞 CDN 的人，只能单独配置一下 cookie-free 域名了。没关系，等咱有了钱，服务器买 20 个，什么联通、电信、铁通、长宽、移动、电信通，通通给接上，带宽怎么着也得 20M，还得上下行速率对等。


好了不废话了，下面使用专业的语言描述一下要干的事：


前提条件：一台自己的服务器（VPS），安装了 nginx，已经有可用的 nginx 配置文件（假定 server_name 为 www.whatever.com），网站运转正常。
待办事项：修改 nginx 的配置文件，让 nginx 监听 cdn.whatever.com，并修改部分对静态文件的处理规则。


## 具体步骤


首先你要增加一个子域名（cdn.whatever.com）指向你的服务器，这个不用多说了吧……
然后需要编辑你的 nginx 配置文件，它原来大概可能长这样


``` javascript
server {
        listen      80;
        server_name www.whatever.com whatever.com;
        root        /root/to/your/site;
    
        location / {
            index   index.html index.php;
        }

    location ~* \\.(gif|jpg|png)$ {
            expires 30d;
        }

    /*此处略去若干*/
}
```



你需要做的是首先修改 server_name 字段，让 nginx 同时监听 cdn.whatever.com。
``` javascript
server_name www.whatever.com whatever.com cdn.whatever.com;
```



然后新增一个 location 块，写如下内容，并确保这个 location 块处于所有已存在的 location 块之前，即 nginx 需要优先处理这个 location 中的内容。
``` javascript
location ~* \\.(?:js|css|png|jpg|jpeg|gif|ico)$ {
        expires max;
        add_header Pragma public;
        add_header Cache-Control "public, must-revalidate, proxy-revalidate";
        access_log off;
        log_not_found off;
        tcp_nodelay off;
        break;
    }
```

    

搞定后保存 nginx 配置文件，然后重启 nginx。


最后一步，将网页中引用的所有 www.whatver.com 域下的静态文件通通改为 cdn.whatever.com。以上文所说的那个首页为例，新的图片地址为 http://cdn.whatver.com/images/[0-9].jpg。

    

## 小记
其实要实现 cookie-free，只需要换一个域名并保证当前页面没有给根域名设置 cookie 即可。比如我们虽然启用了 cdn.whatever.com，但是由于 Google Analytics（以下简称 GA） 设置 cookie 的域为 .whatever.com 而不是 .www.ppios.com，会导致请求 cdn.whatever.com 中的内容时依然会发送 cookie。具体 cookie 的设置情况请自行打开开发者工具查看。


这里再提一下关于限制 GA 设置 cookie 的内容，原则上 GA 应该是默认只设置当前域名的，比如你的页面 URL 是 http://www.whatever.com，则 GA 设置 cookie 的域就是 www.whatever.com；同理如果你的 URL 是 http://whatever.com，则 GA 会把 cookie 设置为 .whatever.com，这样你所有的子域名都会被这个 cookie 影响了。


解决方法就是修改 GA 统计代码，在


``` javascript
_gaq.push(['_setAccount', 'UA-123456788-1']);
_gaq.push(['_trackPageview']);
```

这两行之前新增一行


``` javascript
_gaq.push(['_setDomainName', 'www.whatever.com']);
```



这样就是告诉 GA 只准在 www.whatever.com 域下设置 cookie，关于这个设置顺序折腾了小半个晚上，大家一定要注意啊。