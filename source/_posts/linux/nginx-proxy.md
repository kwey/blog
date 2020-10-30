---
title: Nginx 反向代理与负载均衡
tags: 'nginx'
categories: 'Linux'
top_img: '/img/linux.jpg'
---

## nginx负载均衡的5种策略


<a href="https://www.jianshu.com/p/e98e84a3322f" target="_blank">Nginx入门Nginx 配置反向代理服务器</a>

用户访问网站的时候首先会访问nginx服务器，然后nginx服务器再从服务器集群中选择压力较小的服务器，将该访问请求引向该服务器
## nginx配置
下面修改配置方面我就从mac系统下来进行简单的演示，如何安装的话也暂以mac为主了，windows系统直接去Nginx官网下载安装即可
``` bash
安装nginx
    1-进到homebrew官网，然后复制命令，预安装需要的东西
    2-brew install nginx    安装nginx
    3-nginx -v  显示版本号
进入nginx
    cd /usr/local/etc/nginx
```
## nginx常用命令
<ul><li>
一、启动nginx</li><li>
nginx</li></ul>
    访问localhost:8080(默认)即可
<ul><li>
二、关闭nginx</li><li>
如果出现下图情况，不要惊慌，是因为之前nginx被启动过了</li><li>
只需nginx -s stop，停止nginx服务</li><li>
然后再次启动nginx即可</li></ul><ul><li>
三、重启nginx</li><li>
nginx -s reload</li><li>
每次修改完.conf文件就需要重启nginx</li><li>
四、检查配置</li><li>
检查修改的nginx.conf配置是否正确</li><li>
nginx -t</li><li>
如果出现下面ok和successfull就代表正确了，其他的都不对</li></ul>

nginx: the configuration file /usr/local/etc/nginx/nginx.conf syntax is ok
nginx: configuration file /usr/local/etc/nginx/nginx.conf test is successful

## proxy_pass
nginx反向代理主要通过proxy_pass来配置，将你项目的开发机地址填写到proxy_pass后面，正常的格式为proxy_pass URL即可
``` bash
server {
    listen 80;
    location / {
            proxy_pass http://10.10.10.10:20186;
    }
}
```

## Upstream模块实现负载均衡
<ul><li>
ip_hash指令</li><li>
server指令</li><li>
upstream指令及相关变量</li></ul>
上面写的三个指令，我们直接通过代码来一一分析

``` bash
// 修改nginx.conf
worker_processes 1;
events {
        worker_connections 1024;
}
http {
        upstream firstdemo {
            server 39.106.145.33;
        server 47.93.6.93;
    }
    server {
            listen 8080;
        location / {
                proxy_pass http://firstdemo;
        }
    }
}
```

上面修改的nginx.conf就是上图中花圈的那个文件，nginx配置的主要修改就在这里。化繁为简，把原本nginx.conf里的内容直接替换为上面的不到20行的代码了
既然不到20行，那就把里面对应的内容统统解释一下吧，有个了解就好<ul><li>
worker_processes</li><li>
工作进程数，和CPU核数相同</li><li>
worker_connections</li><li>
每个进程允许的最大连接数</li><li>
upstream模块</li><li>
负载均衡就靠它</li><li>
语法格式：upstream name {}</li><li>
里面写的两个server分别对应着不同的服务器</li><li>
server模块</li><li>
实现反向代理</li><li>
listen监督端口号</li><li>
location / {}访问根路径</li><li>
proxy_pass http://firstdemo，代理到firstdemo里两个服务器上</li></ul>
上面修改了nginx.conf之后，别忘了最重要的一步重启nginx
那么再次访问localhost:8080（打开多个页面会发现访问的服务器内容不同）
每次刷新都会访问不同的服务器，这样就做到了负载均衡处理
不过，更应该做到的是当用户第一次访问到其中一台服务器后，下次再访问的时候就直接访问该台服务器就好了，不用总变化了。那么就发挥了ip_hash的威力了
``` bash
// 省略...
    upstream firstdemo {
        ip_hash;
        server 39.106.145.33;
        server 47.93.6.93;
    }
```

ip_hash它的作用是如果第一次访问该服务器后就记录，之后再访问都是该服务器了，这样比如第一次访问是33服务器，那之后再访问也会分配为33服务器访问了

## 工作中的简单使用
在公司开发项目的时候，遇到设计，产品走查环节的时候，不能每次都让他们去配一个host，毕竟这样不友好，走查起来有麻烦。所以更应该给他们直观的感受，既给一个访问地址就可以看到样子
下面给大家看一下，我正常在公司时nginx做的反向代理配置，和咱们上面的如出一辙，只是加了一个server_name，用指定的域名去访问即可
``` bash
server {
    listen       80;
    server_name  www.demo.com ;
    auth_basic off;
    location / {
        proxy_pass    http://10.10.10.10:20186;
        proxy_set_header Host $host;
        proxy_redirect off;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_connect_timeout 60;
        proxy_read_timeout 600;
        proxy_send_timeout 600;
    }
}
```

每次修改完nginx配置后不要忘记重启nginx才能生效，这样只需要访问www.demo.com这个地址就可以查看我的开发环境，进行走查了。

## <a href="https://segmentfault.com/a/1190000014483200">nginx负载均衡的5种策略</a></h1>


### 1、轮询（默认）
每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器down掉，能自动剔除。
``` bash
upstream backserver {
    server 192.168.0.14;
    server 192.168.0.15;
}

```
## 2、weight
指定轮询几率，weight和访问比率成正比，用于后端服务器性能不均的 
情况。
``` bash
upstream backserver {
    server 192.168.0.14 weight=3;
    server 192.168.0.15 weight=7;
}
```

权重越高，在被访问的概率越大，如上例，分别是30%，70%。

### 3、ip_hash

上述方式存在一个问题就是说，在负载均衡系统中，假如用户在某台服务器上登录了，那么该用户第二次请求的时候，因为我们是负载均衡系统，每次请求都会重新定位到服务器集群中的某一个，那么`已经登录某一个服务器的用户再重新定位到另一个服务器，其登录信息将会丢失，这样显然是不妥的`。

我们可以采用`ip_hash`指令解决这个问题，如果客户已经访问了某个服务器，当用户再次访问时，会将该请求通过`哈希算法，自动定位到该服务器`。

每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器，可以解决`session的问题`。
``` bash
upstream backserver {
    ip_hash;
    server 192.168.0.14:88;
    server 192.168.0.15:80;
}

```
## 4、fair（第三方）
按后端服务器的响应时间来分配请求，响应时间短的优先分配。
``` bash
upstream backserver {
        server server1;
    server server2;
    fair;
}

```
## 5、url_hash（第三方）
按访问url的hash结果来分配请求，使每个url定向到同一个（对应的）后端服务器，后端服务器为缓存时比较有效。
``` bash
upstream backserver {
    server squid1:3128;
    server squid2:3128;
    hash $request_uri;
    hash_method crc32;
}

```

在需要使用负载均衡的server中增加
``` bash
proxy_pass http://backserver/; 
upstream backserver{ 
    ip_hash; 
    server 127.0.0.1:9090 down; (down 表示单前的server暂时不参与负载) 
    server 127.0.0.1:8080 weight=2; (weight 默认为1.weight越大，负载的权重就越大) 
    server 127.0.0.1:6060; 
    server 127.0.0.1:7070 backup; (其它所有的非backup机器down或者忙的时候，请求backup机器) 
} 

```

max_fails ：允许请求失败的次数默认为1.当超过最大次数时，返回proxy_next_upstream 模块定义的错误
fail_timeout:max_fails次失败后，暂停的时间
配置实例：

``` bash
worker_processes  4;
events {
    # 最大并发数
worker_connections  1024;
}
http{
        # 待选服务器列表
    upstream myproject{
         # ip_hash指令，将同一用户引入同一服务器。
        ip_hash;
        server 125.219.42.4 fail_timeout=60s;
        server 172.31.2.183;
    }

    server{
            # 监听端口
        listen 80;
        # 根目录下
        location / {
            # 选择哪个服务器列表
            proxy_pass http://myproject;
        }

    }
}

#user  nobody;

worker_processes  4;
events {
    # 最大并发数
worker_connections  1024;
}
http{
        # 待选服务器列表
    upstream myproject{
            # ip_hash指令，将同一用户引入同一服务器。
        ip_hash;
        server 125.219.42.4 fail_timeout=60s;
        server 172.31.2.183;
    }

    server{
            # 监听端口
        listen 80;
        # 根目录下
        location / {
            # 选择哪个服务器列表
            proxy_pass http://myproject;
        }

    }
}
```
