---
title: django2.2版本中pymysql报错
tags: 'django'
categories: 'python'
top_img: '/img/python.jpg'
---

我们从浏览器发出一个请求 Request，得到一个响应后的内容 HttpResponse ，这个请求传递到 Django的过程如下：



![](/img/middleware.jpg)

也就是说，每一个请求都是先通过中间件中的 process_request 函数，这个函数返回 None 或者 HttpResponse 对象，如果返回前者，继续处理其它中间件，如果返回一个 HttpResponse，就处理中止，返回到网页上。

中间件不用继承自任何类（可以继承 object ），下面一个中间件大概的样子：

``` python

class CommonMiddleware(object):
    def process_request(self, request):
        return None
 
    def process_response(self, request, response):
        return response
```
还有 process_view, process_exception 和 process_template_response 函数。


## 一，比如我们要做一个  拦截器，发现有恶意访问网站的人，就拦截他！

假如我们通过一种技术， 比如统计一分钟访问页面数，太多就把他的 IP 加入到黑名单 BLOCKED_IPS

``` python

#项目 md 文件名 md/middleware.py
 
class BlockedIpMiddleware(object):
    def process_request(self, request):
        if request.META['REMOTE_ADDR'] in getattr(settings, "BLOCKED_IPS", []):
            return http.HttpResponseForbidden('&lt;h1&gt;Forbidden&lt;/h1&gt;')
```
这里的代码的功能就是 获取当前访问者的 IP (request.META['REMOTE_ADDR'])，如果这个 IP 在黑名单中就拦截，如果不在就返回 None (函数中没有返回值其实就是默认为 None)，把这个中间件的 Python 路径写到settings.py中
``` python

MIDDLEWARE = [
        ...
    'md.middleware.BlockedIpMiddleware',
]
```

Django 会从 MIDDLEWARE_CLASSES 或 MIDDLEWARE 中按照从上到下的顺序一个个执行中间件中的 process_request 函数，而其中 process_response 函数则是最前面的最后执行。如上图所示


