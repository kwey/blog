---
title: 前端开发者必备的Nginx知识
tags: 'nginx'
categories: 'Linux'
top_img: '/img/linux.jpg'
---

> nginx是一个高性能的HTTP和反向代理服务器，也是一个通用的TCP/UDP代理服务器，最初由俄罗斯人Igor Sysoev编写

nginx在应用程序中的作用<ul><li>解决跨域</li><li>请求过滤</li><li>配置gzip</li><li>负载均衡</li><li>静态资源服务器</li><li>...</li></ul>

nginx现在几乎是众多大型网站的必用技术，大多数情况下，我们不需要亲自去配置它，但是了解它在应用程序中所担任的角色，以及如何解决这些问题是非常必要的。

下面我将从nginx在企业中的真实应用来解释nginx在应用程序中起到的作用。

为了便于理解，首先先来了解一下一些基础知识，nginx是一个高性能的反向代理服务器那么什么是反向代理呢？
<a href="https://www.webq.top/article/5ca0e011bc22ce6603871671" target="_blank">正反向代理看这</a>

# 基本配置<h4>配置结构
下面是一个nginx配置文件的基本结构：

``` bash
events { }

http {
    server { 
        location path
        {
            ...
        }
        location path
        {
            ...
        }
    }

    server {
        ...
    }

}

```
<ul><li>main:nginx的全局配置，对全局生效。</li><li>events:配置影响nginx服务器或与用户的网络连接。</li><li>http：可以嵌套多个server，配置代理，缓存，日志定义等绝大多数功能和第三方模块的配置。</li><li>server：配置虚拟主机的相关参数，一个http中可以有多个server。</li><li>location：配置请求的路由，以及各种页面的处理情况。</li><li>upstream：配置后端服务器具体地址，负载均衡配置不可或缺的部分。</li></ul><h4>内置变量
下面是nginx一些配置中常用的内置全局变量，你可以在配置的任何位置使用它们。<table><thead><tr><th>变量名</th><th>功能</th></tr></thead><tbody><tr><td>$host</td><td>请求信息中的Host，如果请求中没有Host行，则等于设置的服务器名</td></tr><tr><td>$request_method</td><td>客户端请求类型，如GET、POST</td></tr><tr><td>$remote_addr</td><td>客户端的IP地址</td></tr><tr><td>$args</td><td>请求中的参数</td></tr><tr><td>$content_length</td><td>请求头中的Content-length字段</td></tr><tr><td>$http_user_agent</td><td>客户端agent信息</td></tr><tr><td>$http_cookie</td><td>客户端cookie信息</td></tr><tr><td>$remote_port</td><td>客户端的端口</td></tr><tr><td>$server_protocol</td><td>请求使用的协议，如HTTP/1.0、·HTTP/1.1`</td></tr><tr><td>$server_addr</td><td>服务器地址</td></tr><tr><td>$server_name</td><td>服务器名称</td></tr><tr><td>$server_port</td><td>服务器的端口号</td></tr></tbody></table>
# 解决跨域
先追本溯源以下，跨域究竟是怎么回事。<h4>跨域的定义
同源策略限制了从同一个源加载的文档或脚本如何与来自另一个源的资源进行交互。这是一个用于隔离潜在恶意文件的重要安全机制。通常不允许不同源间的读操作。<h4>同源的定义
如果两个页面的协议，端口（如果有指定）和域名都相同，则两个页面具有相同的源
<h4>nginx解决跨域的原理
例如：<ul><li>前端server的域名为：fe.server.com</li><li>后端服务的域名为：dev.server.com</li></ul>
现在我在fe.server.com对dev.server.com发起请求一定会出现跨域。

现在我们只需要启动一个nginx服务器，将server_name设置为fe.server.com,然后设置相应的location以拦截前端需要跨域的请求，最后将请求代理回dev.server.com。如下面的配置：
```
server {
    listen       80;
    server_name  fe.server.com;
    location / {
            proxy_pass dev.server.com;
    }
}

```

这样可以完美绕过浏览器的同源策略：fe.server.com访问nginx的fe.server.com属于同源访问，而nginx对服务端转发的请求不会触发浏览器的同源策略。

# 请求过滤

根据状态码过滤
```
error_page 500 501 502 503 504 506 /50x.html;
    location = /50x.html {
        #将跟路径改编为存放html的路径。
        root /root/static/html;
    }
        
```

根据URL名称过滤，精准匹配URL，不匹配的URL全部重定向到主页。
```
location / {
    rewrite  ^.*$ /index.html  redirect;
}
    
```

根据请求类型过滤。
```
if ( $request_method !~ ^(GET|POST|HEAD)$ ) {
    return 403;
}
        
```

# 配置gzip

GZIP是规定的三种标准HTTP压缩格式之一。目前绝大多数的网站都在使用GZIP传输 HTML、CSS、JavaScript 等资源文件。
对于文本文件，GZip 的效果非常明显，开启后传输所需流量大约会降至 1/4 ~ 1/3。
并不是每个浏览器都支持gzip的，如何知道客户端是否支持gzip呢，请求头中的Accept-Encoding来标识对压缩的支持。
    
启用gzip同时需要客户端和服务端的支持，如果客户端支持gzip的解析，那么只要服务端能够返回gzip的文件就可以启用gzip了,我们可以通过nginx的配置来让服务端支持gzip。下面的respone中content-encoding:gzip，指服务端开启了gzip的压缩方式。
<figure><img src="https://user-gold-cdn.xitu.io/2019/3/11/1696a1190112985c?imageView2/0/w/1280/h/960/format/webp/ignore-error/1">
<figcaption></figcaption></figure>

```
    gzip                    on;
    gzip_http_version       1.1;        
    gzip_comp_level         5;
    gzip_min_length         1000;
    gzip_types             text/csv text/xml text/css text/plain text/javascript 
                            application/javascript application/x-javascript 
                            application/json application/xml;
                            
```
<h4>gzip<ul><li>开启或者关闭gzip模块</li><li>默认值为off</li><li>可配置为on / off</li></ul><h4>gzip_http_version<ul><li>启用 GZip 所需的HTTP 最低版本</li><li>默认值为HTTP/1.1</li></ul>
这里为什么默认版本不是1.0呢？
HTTP 运行在TCP 连接之上，自然也有着跟TCP 一样的三次握手、慢启动等特性。
启用持久连接情况下，服务器发出响应后让TCP连接继续打开着。同一对客户/服务器之间的后续请求和响应可以通过这个连接发送。

为了尽可能的提高 HTTP 性能，使用持久连接就显得尤为重要了。
HTTP/1.1默认支持TCP持久连接，HTTP/1.0 也可以通过显式指定 Connection: keep-alive 来启用持久连接。对于TCP持久连接上的HTTP 报文，客户端需要一种机制来准确判断结束位置，而在 HTTP/1.0中，这种机制只有Content-Length。而在HTTP/1.1中新增的 Transfer-Encoding: chunked 所对应的分块传输机制可以完美解决这类问题。
nginx同样有着配置chunked的属性chunked_transfer_encoding，这个属性是默认开启的。
Nginx在启用了GZip的情况下，不会等文件 GZip 完成再返回响应，而是边压缩边响应，这样可以显著提高 TTFB(Time To First Byte，首字节时间，WEB 性能优化重要指标)。这样唯一的问题是，Nginx 开始返回响应时，它无法知道将要传输的文件最终有多大，也就是无法给出Content-Length这个响应头部。
所以，在HTTP1.0中如果利用Nginx启用了GZip，是无法获得Content-Length的，这导致HTTP1.0中开启持久链接和使用GZip只能二选一，所以在这里gzip_http_version默认设置为1.1。<h4>gzip_comp_level<ul><li>压缩级别，级别越高压缩率越大，当然压缩时间也就越长（传输快但比较消耗cpu）。</li><li>默认值为 1</li><li>压缩级别取值为1-9</li></ul><h4>gzip_min_length<ul><li>设置允许压缩的页面最小字节数，Content-Length小于该值的请求将不会被压缩</li><li>默认值:0</li><li>当设置的值较小时，压缩后的长度可能比原文件大，建议设置1000以上</li></ul><h4>gzip_types<ul><li>要采用gzip压缩的文件类型(MIME类型)</li><li>默认值:text/html(默认不压缩js/css)</li></ul>

# 负载均衡<h4>nginx如何实现负载均衡
Upstream指定后端服务器地址列表

```
upstream balanceServer {
    server 10.1.22.33:12345;
    server 10.1.22.34:12345;
    server 10.1.22.35:12345;
}
    
```

在server中拦截响应请求，并将请求转发到Upstream中配置的服务器列表。
```
    server {
        server_name  fe.server.com;
        listen 80;
        location /api {
            proxy_pass http://balanceServer;
        }
    }
    
```

上面的配置只是指定了nginx需要转发的服务端列表，并没有指定分配策略。<h4>nginx实现负载均衡的策略

轮询策略
默认情况下采用的策略，将所有客户端请求轮询分配给服务端。这种策略是可以正常工作的，但是如果其中某一台服务器压力太大，出现延迟，会影响所有分配在这台服务器下的用户。
```
upstream balanceServer {
    server 10.1.22.33:12345;
    server 10.1.22.34:12345;
    server 10.1.22.35:12345;
}
    
```


最小连接数策略
将请求优先分配给压力较小的服务器，它可以平衡每个队列的长度，并避免向压力大的服务器添加更多的请求。
```
upstream balanceServer {
    least_conn;
    server 10.1.22.33:12345;
    server 10.1.22.34:12345;
    server 10.1.22.35:12345;
}
    
```


最快响应时间策略
依赖于NGINX Plus，优先分配给响应时间最短的服务器。
```
upstream balanceServer {
    fair;
    server 10.1.22.33:12345;
    server 10.1.22.34:12345;
    server 10.1.22.35:12345;
}
    
```

客户端ip绑定
来自同一个ip的请求永远只分配一台服务器，有效解决了动态网页存在的session共享问题。
```
upstream balanceServer {
    ip_hash;
    server 10.1.22.33:12345;
    server 10.1.22.34:12345;
    server 10.1.22.35:12345;
}

```

# 静态资源服务器
```
location ~* \\.(png|gif|jpg|jpeg)$ {
    root    /root/static/;  
    autoindex on;
    access_log  off;
    expires     10h;# 设置过期时间为10小时          
}
    
```

匹配以png|gif|jpg|jpeg为结尾的请求，并将请求转发到本地路径，root中指定的路径即nginx本地路径。同时也可以进行一些缓存的设置。
