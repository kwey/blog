---
title: cookie-parser 和 express-session
tags: 'http'
categories: 'brower'
top_img: '/img/brower.jpeg'
---
> cookie-parser 在用 express 生成器构建项目时自动安装的，它的作用就是设置，获取和删除 cookie。express-session 依赖于它。


 ## cookie-parser
### 1. 引入

``` javascript
var cookieParser = require('cookie-parser');    #引入模块
app.use(cookieParser());        #挂载中间件，可以理解为实例化

```

这两行代码默认在app.js中写好了，表示现在可以直接使用 cookie 了。

## 2. 创建cookie
```
res.cookie(name, value [, options]);
```

name 是 cookie 名，value 是 cookie 值，可以是 json 对象或字符串。options 是选项，详细请阅<a href="http://www.expressjs.com.cn/4x/api.html#res.cookie" rel="nofollow noreferrer" target="_blank">这里</a>，常用选项有：
```
{
    'maxAge': 90000,    # 有效时长，即90000毫秒后过期，String
    'signed': false     # 默认为false，表示是否签名，Boolean
}
```

创建 cookie 会在 http 响应头中添加Set-Cookie，从而在浏览器中设置 cookie<h3 id="articleHeader3">3. 获取cookie
```
var cookies = req.cookies      # 获取cookie集合
var value = req.cookies.key    # 获取名称为key的cookie的值
```
## 4. 删除cookie
```javascript
res.clearCookie(name [, options])
```

name 是 cookie 名，options 与创建 cookie 时所传一致

## 5. 签名
上文所写 cookie 的各种操作，都是没有经过签名的。签名可以提高安全性。下面是使用签名生成 cookie的方法，大同小异，修改上文即可
```javascript
app.use(cookieParser('ruidoc')); 
//  需要传一个自定义字符串作为secret
```

```javascript
// # 创建cookie的options中，必填 signed: true
res.cookie(name, value, {    
        'signed': true
    });
```

```javascript
var cookies = req.signedCookies      # 获取cookie集合
var value = req.signedCookies.key    # 获取名称为key的cookie的值
```

提示：使用签名时这三处必须一起修改，只改一处是无效的！

## 6. 栗子
```javascript
// 设置cookie名为user，值为对象，90000ms过期，无签名
res.cookie('user', {
        id: 1,
        name: 'ruidoc'
    }, {
        maxAge: 900000 
    }
);

//获取设置的cookie
var user = req.cookies.user
// 设置cookie名为admin，值为对象，无过期时间，有签名
res.cookie('admin', { 'id': 1 }, {
        'signed': true
    }
);

//获取设置的cookie
var admin = req.signedCookies.admin
```

options 中没有设置过期时间的话，默认关闭浏览器即过期清除

## express-session
express-session 才是真正在服务端保存数据的中间件，它需要独立安装
```bash
npm install express-session --save
```

然后在app.js中引入，在app.use(cookieParser()) 之后挂载
```javascript
var session = require('express-session');
app.use(session([options]));
```

同样说几个常用的options
```javascript
{
    'secret': 'ruidoc',     # 签名，与上文中cookie设置的签名字符串一致，
    'cookie': {
        'maxAge': 90000
    },
    'name': 'session_id'    # 在浏览器中生成cookie的名称key，默认是connect.sid
}
```

因为创建 session 的同时会创建 cookie 来保存 sessionId，所以options 中的 cookie.maxAge 可看作是 session 的有效时长。

##  创建session
```javascript
# 创建一个session
req.session.key = value

# 创建多个session
req.session = {
    key1: value1,
    key2: value2
}
```
## 获取session
```javascript
var session = req.session      # 获取session集合
var value = req.session.key    # 获取名称为key的session的值
```
## 销毁session

```javascript
// 清空所有session
req.session.key.destroy()    # 销毁名称为key的session的值" title="">
req.session.destroy()      # 清空所有session
req.session.key.destroy()    # 销毁名称为key的session的值
```
<a href="https://segmentfault.com/a/1190000009663833" target="_blank">原文地址</a>