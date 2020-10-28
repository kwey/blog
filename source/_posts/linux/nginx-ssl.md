---
title: Linux Nginx ssl证书部署
tags: 'nginx'
categories: 'Linux'
top_img: '../../img/linux.jpg'
---

## Nginx ssl证书部署,反向代理本地服务


<a href="https://yq.aliyun.com/articles/637307" target="_blank">最新阿里云申请免费SSL证书实现网站HTTPS化（图文教程一）</a>

<a href="https://juejin.im/entry/57fb07b0816dfa0056c0ada8" target="_blank">Nginx 反向代理详解</a>

<a href="https://www.jianshu.com/p/208c02c9dd1d" target="_blank">正向代理与反向代理的区别</a>

<table><tbody><tr><td align="center">listen 443</td><td align="center">SSL访问端口号为443</td></tr><tr><td align="center">ssl on</td><td align="center">启用SSL功能</td></tr><tr><td align="center">ssl_certificate</td><td align="center">证书文件</td></tr><tr><td align="center">ssl_certificate_key</td><td align="center">私钥文件</td></tr><tr><td align="center">ssl_protocols</td><td align="center">使用的协议</td></tr><tr><td align="center">ssl_ciphers</td><td align="center">配置加密套件，写法遵循openssl标准</td></tr></tbody></table>

```
server {
  listen       443;
    server_name  www.domain.com;
    ssl on;
    ssl_certificate /home/a.pem;
    ssl_certificate_key /home/a.key;
    ssl_session_timeout 5m;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
    ssl_prefer_server_ciphers on;
  
    location / {
      proxy_pass http://127.0.0.1:3000;
      proxy_http_version 1.1;
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection ‘upgrade’;
      proxy_set_header Host $host;
      proxy_cache_bypass $http_upgrade;
    }
  }
```
