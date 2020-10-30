---
title: Express中间件
tags: 'node'
categories: 'Node'
top_img: '/img/node.jpg'
---

> Express 是一个路由和中间件 Web 框架，其自身只具有最低程度的功能：Express 应用程序基本上是一系列中间件函数调用。

中间件函数能够访问<a href="https://expressjs.com/zh-cn/4x/api.html#req">请求对象</a> (req)、<a href="https://expressjs.com/zh-cn/4x/api.html#res">响应对象</a> (res) 以及应用程序的请求/响应循环中的下一个中间件函数。下一个中间件函数通常由名为 next 的变量来表示。

中间件函数可以执行以下任务：<ul><li><执行任何代码。</li><li><对请求和响应对象进行更改。</li><li><结束请求/响应循环。</li><li><调用堆栈中的下一个中间件函数。</li></ul>
如果当前中间件函数没有结束请求/响应循环，那么它必须调用 next()，以将控制权传递给下一个中间件函数。否则，请求将保持挂起状态。

Express 应用程序可以使用以下类型的中间件：<ul><li><a href="https://expressjs.com/zh-cn/guide/using-middleware.html#middleware.application" style="color: rgb(70, 172, 200); font-size: large;">应用层中间件</a></li><li><a href="https://expressjs.com/zh-cn/guide/using-middleware.html#middleware.router" style="color: rgb(70, 172, 200); font-size: large;">路由器层中间件</a></li><li><a href="https://expressjs.com/zh-cn/guide/using-middleware.html#middleware.error-handling" style="color: rgb(70, 172, 200); font-size: large;">错误处理中间件</a></li><li><a href="https://expressjs.com/zh-cn/guide/using-middleware.html#middleware.built-in" style="color: rgb(70, 172, 200); font-size: large;">内置中间件</a></li><li><a href="https://expressjs.com/zh-cn/guide/using-middleware.html#middleware.third-party" style="color: rgb(70, 172, 200); font-size: large;">第三方中间件</a></li></ul>
## 应用层中间件:
此示例显示没有安装路径的中间件函数。应用程序每次收到请求时执行该函数。
``` javascript

var app = express();
app.use(function (req, res, next) {
    console.log('Time:', Date.now());
    next();
});
```

此示例显示安装在 /user/:id 路径中的中间件函数。在 /user/:id 路径中为任何类型的 HTTP 请求执行此函数。
```javascript
app.use('/user/:id', function (req, res, next) {
    console.log('Request Type:', req.method);
    next();
});
```

此示例显示一个路由及其处理程序函数（中间件系统）。此函数处理针对 /user/:id 路径的 GET 请求。
```javascript
app.get('/user/:id', function (req, res, next) {
    res.send('USER');
});

```

以下是在安装点使用安装路径装入一系列中间件函数的示例。 它演示一个中间件子堆栈，用于显示针对 /user/:id 路径的任何类型 HTTP 请求的信息。
```javascript
app.use('/user/:id', function(req, res, next) {
    console.log('Request URL:', req.originalUrl);
    next();
}, function (req, res, next) {
    console.log('Request Type:', req.method);
    next();
});
```

## 路由器层中间件

```javascript
var app = express();
var router = express.Router();

// a middleware function with no mount path. This code is executed for every request to the router
router.use(function (req, res, next) {
        console.log('Time:', Date.now());
        next();
    });

// a middleware sub-stack shows request info for any type of HTTP request to the /user/:id path
    router.use('/user/:id', function(req, res, next) {
        console.log('Request URL:', req.originalUrl);
        next();
    }, function (req, res, next) {
        console.log('Request Type:', req.method);
        next();
    });

// a middleware sub-stack that handles GET requests to the /user/:id path
    router.get('/user/:id', function (req, res, next) {
        // if the user ID is 0, skip to the next router
        if (req.params.id == 0) next('route');
        // otherwise pass control to the next middleware function in this stack
        else next();//
    }, function (req, res, next) {
        // render a regular page
        res.render('regular');
    });
```

## 错误处理中间件

错误处理中间件函数的定义方式与其他中间件函数基本相同，差别在于错误处理函数有四个自变量而不是三个，专门具有特征符 (err, req, res, next)：
```

app.use(function(err, req, res, next) {
        console.error(err.stack);
        res.status(500).send('Something broke!');
    });

```
## 内置中间件
### express.static(root, [options])
Express 中唯一内置的中间件函数是 express.static。此函数基于 <a href="https://github.com/expressjs/serve-static">serve-static</a>，负责提供 Express 应用程序的静态资源。
root 自变量指定从其中提供静态资源的根目录。
可选的 options 对象可以具有以下属性：<table><thead><tr><th>属性</th><th>描述</th><th>类型</th><th>缺省值</th></tr></thead><tbody><tr><td>dotfiles</td><td>是否对外输出文件名以点（.）开头的文件。有效值包括“allow”、“deny”和“ignore”</td><td>字符串</td><td>“ignore”</td></tr><tr><td>etag</td><td>启用或禁用 etag 生成</td><td>布尔</td><td>true</td></tr><tr><td>extensions</td><td>用于设置后备文件扩展名。</td><td>数组</td><td>[]</td></tr><tr><td>index</td><td>发送目录索引文件。设置为 false 可禁用建立目录索引。</td><td>混合</td><td>“index.html”</td></tr><tr><td>lastModified</td><td>将 Last-Modified 的头设置为操作系统上该文件的上次修改日期。有效值包括 true 或 false。</td><td>布尔</td><td>true</td></tr><tr><td>maxAge</td><td>设置 Cache-Control 头的 max-age 属性（以毫秒或者 <a href="https://www.npmjs.org/package/ms">ms 格式</a>中的字符串为单位）</td><td>数字</td><td>0</td></tr><tr><td>redirect</td><td>当路径名是目录时重定向到结尾的“/”。</td><td>布尔</td><td>true</td></tr><tr><td>setHeaders</td><td>用于设置随文件一起提供的 HTTP 头的函数。</td><td>函数</td><td> </td></tr></tbody></table>
## 第三方中间件
    
使用第三方中间件向 Express 应用程序添加功能。
安装具有所需功能的 Node.js 模块，然后在应用层或路由器层的应用程序中将其加装入。
以下示例演示如何安装和装入 cookie 解析中间件函数 cookie-parser。
```

$ npm install cookie-parser


```

```

var express = require('express');
var app = express();
var cookieParser = require('cookie-parser');

// load the cookie-parsing middleware
app.use(cookieParser());
```



# Express中间件的原理
```javascript
/**
* 仿照express实现中间件的功能
*
* Created by BadWaka on 2017/3/6.
*/

var http = require('http');

/**
* 仿express实现中间件机制
*
* @return {app}
*/
function express() {
    var funcs = []; // 待执行的函数数组
    var app = function (req, res) {
        var i = 0;

        function next() {
            var task = funcs[i++];  // 取出函数数组里的下一个函数
            if (!task) {    // 如果函数不存在,return
                return;
            }
        task(req, res, next);   // 否则,执行下一个函数
    }
    next();
}

/**
* use方法就是把函数添加到函数数组中
* @param task
*/
app.use = function (task) {
    funcs.push(task);
}

return app;    // 返回实例
}

// 下面是测试case

var app = express();
http.createServer(app).listen('3000', function () {
    console.log('listening 3000....');
});

function middlewareA(req, res, next) {
    console.log('middlewareA before next()');
    next();
    console.log('middlewareA after next()');
}

function middlewareB(req, res, next) {
    console.log('middlewareB before next()');
    next();
    console.log('middlewareB after next()');
}

function middlewareC(req, res, next) {
    console.log('middlewareC before next()');
    next();
    console.log('middlewareC after next()');
}

app.use(middlewareA);
app.use(middlewareB);
app.use(middlewareC);
```

middlewareA before next()
    
middlewareB before next()

middlewareC before next()

middlewareC after next()

middlewareB after next()

middlewareA after next()