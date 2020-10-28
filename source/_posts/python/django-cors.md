---
title: Django配置Ajax跨域调用/设置Cookie
tags: 'django'
categories: 'python'
top_img: '../../img/python.jpg'
---
## django-cors-headers


## 一、跨域调用</h3>
首先下载这个插件

``` python
pip install django-cors-headers
```
然后在你的Django项目中的settings.py中做如下配置(install app和middleware)：

``` python
INSTALLED_APPS = (
    ...
    'corsheaders',
    ...
)

MIDDLEWARE = [ # Or MIDDLEWARE_CLASSES on Django &lt; 1.10
    ...
    'corsheaders.middleware.CorsMiddleware',
    'django.middleware.common.CommonMiddleware',
    ...
]
```

要注意的是，CorsMiddleware最好放的尽可能的往上，比如要 在CommonMiddleware和WhiteNoiseMiddleware的上边，要不然设置的跨域响应头可能起不了什么作用。

最后配置一下你允许的跨域请求主机

允许所有主机请求你的API，这种设置下不能设置cookie，下文会说解决方案

``` python
CORS_ORIGIN_ALLOW_ALL = True
```
或者指定白名单。这里注意有个坑，当前后端联机调试的时候，前端在浏览器写localhost或127.0.0.1和本机的具体IP地址在白名单里是不一样的，推荐调试的时候写具体IP地址，白名单里也写具体IP地址。

``` python

CORS_ORIGIN_WHITELIST = (
    '172.30.203.52:8080',
    'localhost:80'
)
```

## 二、携带cookie

要跨域设置cookie，需要前后端的共同努力，首先看后端：

前提是插件下载好了，并且install_app和middleware都配置了。如上边提到的，将CORS_ORIGIN_ALLOW_ALL设为false，通过白名单的方式设定允许跨域请求的请求源，然后再设置CORS_ALLOW_CREDENTIALS配置项为True，如下：


``` python
# If True, cookies will be allowed to be included in cross-site HTTP requests. Defaults to False.
CORS_ALLOW_CREDENTIALS = True
```

前端只需要在发起请求时设置：

```
withCredentials: true
```





