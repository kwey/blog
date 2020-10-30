---
title: Nginx代理ssl转发https和wss请求
tags: 'nginx'
categories: 'Linux'
top_img: '/img/linux.jpg'
---

## 使用场景

什么情况下需要这种操作？

比如，后台多台服务器做反向代理；

比如，微信小程序，建立微信小程序的websocket连接，必须要使用wss，但是目前不支持自定义端口，在调用的时候会使用默认端口，这个时候就需要做个代理。

## 实现
Nginx配置
``` bash
server {
    listen 443; #https和wss协议默认端口
    # ssl的相关配置
    ssl on;
    ssl_certificate /usr/local/a.pem; // ssl pem文件
    ssl_certificate_key /usr/local/a.key; // ssl key文件
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers EECDH+CHACHA20:EECDH+AES128:RSA+AES128:EECDH+AES256:RSA+AES256:EECDH+3DES:RSA+3DES:!MD5;
    ssl_prefer_server_ciphers on;
    ssl_buffer_size 1400;
    add_header Strict-Transport-Security max-age=15768000;
    add_header Cache-Control no-store;
    ssl_stapling on;
    ssl_stapling_verify on;
    server_name www.webq.top;
    # 转发wss协议
    location /wss {
        proxy_pass http://127.0.0.1:2346;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "Upgrade";
        proxy_set_header X-Real-IP $remote_addr;
    }
    # 转发https协议
    location /{
        proxy_pass http://127.0.0.1:2345;
        proxy_set_header   X-Real-IP        $remote_addr;
        proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
        # add_header Access-Control-Allow-Origin *;
    }
}
```

这样，当我们访问 <a href="https://swoole.app/">https://</a>www.webq.top 的时候实际上就转发到了内部的2345端口的http服务，而不需要 <a href="https://swoole.app:9502/">https://www.webq.top:2</a>345 。建立websocket连接时直接与 wss://www.webq.top/wss 建立连接，会转发到内部2346端口的服务。


参考：
<a href="https://www.goozp.com/article/69.html" target="_blank">Nginx代理ssl转发https和wss请求</a>
    