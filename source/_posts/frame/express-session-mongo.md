---
title: express-session之数据保存mongodb
tags: 'model'
categories: 'frame'
top_img: '/img/design.jpeg'
---
> session数据存储空间一般是在内存中开辟的，那么在内存中的session显然是存在极大的数据丢失的隐患的，比如系统掉电，所有的会话数据就会丢失，这里主要介绍session持久化保存到mongoDB的工具connect-mongo。

express-session中间件将会话数据存储在服务器上；它仅将会话标识（而非会话数据）保存在 cookie 中。从1.5.0版本开始, express-session不再依赖cookie-parser,直接通过req/res读取/写入;默认存储位置内存存储(服务器端),
        
        
``` javascript

npm install express-session connect-mongo
 app .use (
    session ({
      secret:   secret ,
      name:   'session_id' ,
      saveUninitialized:   false ,
   // 在存储一些新数据之前，不创建session
      resave:   false ,
   // 如果没有发生任何修改不储存session。
      store:   new   MongoStore ({
        url: 'mongodb://127.0.0.1:27017/k-blog' ,
        touchAfter:   24 * 3600   // 单位是秒
     }),
      cookie:  {
        maxAge: 24 * 3600 * 1000 //单位毫秒
     }
   })
 )
```


 express-session

* 1.、name - cookie的名字（原属性名为 key）。（默认：’connect.sid’）

* 2、 store - session存储实例 
* 3.、secret - 用它来对session cookie签名，防止篡改 
* 4、 cokie - session cookie设置 （默认：{ path: ‘/‘, httpOnly: true,secure: false, maxAge: null }）
* 5、 genid - 生成新session ID的函数 （默认使用uid2库） 
* 6、 rolling - 在每次请求时强行设置cookie，这将重置cookie过期时间（默认：false）
* 7、 resave - 强制保存session即使它并没有变化 （默认： true） 
* 8、 proxy - 当设置了secure cookies（通过”x-forwarded-proto” header ）时信任反向代理。当设定为true时，
”x-forwarded-proto” header 将被使用。当设定为false时，所有headers将被忽略。当该属性没有被设定时，将使用Express的trust proxy。 
* 9、saveUninitialized - 强制将未初始化的session存储。当新建了一个session且未设定属性或值时，它就处于
未初始化状态。在设定一个cookie前，这对于登陆验证，减轻服务端存储压力，权限控制是有帮助的。（默认：true） 
* 10、unset - 控制req.session是否取消（例如通过 delete，或者将它的值设置为null）。这可以使session保持存储
状态但忽略修改或删除的请求（默认：keep）
* express-session的一些方法:
1、ession.destroy():删除session，当检测到客户端关闭时调用。 
2、Session.reload():当session有修改时，刷新session。 
3、Session.regenerate()：将已有session初始化。
4、Session.save()：保存session。

connect-mongo


ttl: 14 * 24 * 60 * 60 //session保存时间，默认14天
autoRemove:'native' //这是一个基本的默认设置。
autoRemoveInterval ： 10 // 单位分钟，当autoRemove为‘interval’定时删除
一：url： 'mongodb://localhost/test-app', // mongodb 地址
collection： 集合名称，默认为sessions


mongoOptions：{}

二：mongooseConnection

三：db 

四：dbPromise : 

touchAfter: 24*3600 //单位是秒

这样只要在24小时内，无论你发多少个请求，session只会被更新一次。


``` javascript

serialize：自定义序列化函数

unserialize：自定义反序列化函数
stringify：默认是true,如果为true则序列化和反序列化使用原生的JSON.xxx处理.
transformId：function将sessionId转为你想要的任何键然后进行储存

```

<a href="https://segmentfault.com/a/1190000017383466" target="_blank" style="font-size: x-large;">二、三、四mongo的连接方法看这</a>