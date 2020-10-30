---
title: 基本的 WebSocket 的 Nginx 配置
tags: 'nginx'
categories: 'Linux'
top_img: '/img/linux.jpg'
---

<a href="https://siriux.net/2013/06/nginx-and-websockets/" target="_blank">nginx and WebSockets</a>
        
先用 ws 模块写一个简单的 WebSocket 服务器:
``` javascript
Server = require('ws').Server

wss = new Server port: 3000

wss.on ('connection', (ws) =>{
    console.log 'a connection'
    ws.send 'started'
})
console.log 'server started'

```

然后修改 Hosts, 添加, 比如 ws.repo, 指向 127.0.0.1
然后是 Nginx 配置:
```javascript
server {
    listen 80;
    server_name ws.repo;

    location / {
        proxy_pass http://127.0.0.1:3000/;
        proxy_redirect off;

        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}

```

Reload Nginx 然后从浏览器控制台尝试链接, OK
```javascript
new WebSocket('ws://ws.repo/')

```

或者通过 Upstream 的写法:
```javascript
upstream ws_server {
    server 127.0.0.1:3000;
}

server {
    listen 80;
    server_name ws.repo;

    location / {
        proxy_pass http://ws_server/;
        proxy_redirect off;

        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}

```

WebSocket 先是通过 HTTP 建立连接,
然后通过 101 状态码, 表示切换协议,, 在配置里是 Upgrade# nginx配置websocket支持wss
```javascript
map $http_upgrade $connection_upgrade {  
    default upgrade;  
    '' close;  
}  
upstream websocket {  
    server 128.190.82.105:8888;  
}  
server {  
    listen 8888;  
    server_name proxy.hello.com;
    ssl on;
    ssl_certificate /etc/nginx/ssl/hello.com_bundle.crt;
    ssl_certificate_key /etc/nginx/ssl/hello.com.key;
    ssl_session_timeout 20m;
    ssl_verify_client off;
    location / {  
        proxy_pass http://websocket;  
        proxy_http_version 1.1;  
        proxy_set_header Upgrade $http_upgrade;  
        proxy_set_header Connection "Upgrade";  
    }  
}
```

128.190.82.105:8888是真正的服务端地址，nginx所在域名是proxy.hello.com，代理的端口号是8888，所以前端访问的时候这样配置：
```
WEBSOCKET_URL: 'wss://proxy.hello.com:8888',  
```

![](/img/ws.webp)