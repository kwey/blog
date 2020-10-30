---
title: XSS和CSRF
tags: 'es6'
categories: 'Javascript'
top_img: '../../img/js.jpg'
---

> 跨站脚本攻击(Cross Site Scripting)、跨站请求伪造(Cross Site Request Forgery)

XSS，(cross site scripting),跨站脚本注入，指攻击者利用一些技巧向页面注入脚本代码并让其执行，从而达成攻击效果。
        
## XSS原理
攻击者利用页面输入和输出的功能，在输入时使用闭标签再加脚本代码等技巧，当服务器输出这些输入信息到页面时，就会插入并执行脚本代码。解决方法是可以在服务端对客户端的输入进行过滤或转义。注意：这里不一定只用script标签，还可以用img等其他标签，或者用onclick等事件方法达到执行脚本代码。简单例子如下：
``` javascript
//Web 请求如下所示：
GET http://www.somesite.com/page.asppageid=10&lang=en&title=Section%20Title
//在发出请求后，服务器返回的 HTML 内容包括：

// Section Title
// 攻击者可以通过摆脱 
//  标记来注入代码：
// http://www.somesite.com/page.asp?pageid=10&lang=en&title=Section%20Title
// <script>alert(‘XSS%20attack’)</script>
//最终这个请求的 HTML 输出将为：

// Section Title<script>alert(‘XSS attack’)</script>

```


## XSS危害与防范
攻击者可以使用 XSS 漏洞搞恶作剧，窃取 Cookie，劫持帐户，执行 ActiveX，执行 Flash 内容，强迫您下载软件，官网挂钓鱼网站，或者是对硬盘和数据采取操作。防范的方法就是服务端对能被攻击的输入进行过滤或转义。

## CSRF
XSS 是实现 CSRF 的诸多途径中的一条，但绝对不是唯一的一条。一般习惯上把通过 XSS 来实现的 CSRF 称为 XSRF。
CSRF 的全称是“跨站请求伪造”，而 XSS 的全称是“跨站脚本”。看起来有点相似，它们都是属于跨站攻击——不攻击服务器端而攻击正常访问网站的用户


## CSRF 并不一定要有站内的输入，因为它并不属于注入攻击，而是请求伪造。被伪造的请求可以是任何来源，而非一定是站内。所以我们唯有一条路可行，就是过滤请求的 处理者。
![](/img/xss.png)


## CSRF攻击的防御

目前业界服务器端防御CSRF攻击主要有三种策略：验证HTTP Referer字段，在请求地址中添加token并验证，在HTTP头中自定义属性并验证。下面分别对这三种策略进行简要介绍。

## 1、 验证HTTP Referer字段
根据HTTP协议，在HTTP头中有一个字段叫Referer，它记录了该HTTP请求的来源地址。在通常情况下，访问一个安全受限页面的请求必须来自于同一个网站。比如某银行的转账是通过用户访问http://bank.test/test?page=10&userID=101&money=10000页面完成，用户必须先登录bank. test，然后通过点击页面上的按钮来触发转账事件。当用户提交请求时，该转账请求的Referer值就会是转账按钮所在页面的URL（本例中，通常是以bank. test域名开头的地址）。而如果攻击者要对银行网站实施CSRF攻击，他只能在自己的网站构造请求，当用户通过攻击者的网站发送请求到银行时，该请求的Referer是指向攻击者的网站。因此，要防御CSRF攻击，银行网站只需要对于每一个转账请求验证其Referer值，如果是以bank. test开头的域名，则说明该请求是来自银行网站自己的请求，是合法的。如果Referer是其他网站的话，就有可能是CSRF攻击，则拒绝该请求。

## 2、 在请求地址中添加token并验证
CSRF攻击之所以能够成功，是因为攻击者可以伪造用户的请求，该请求中所有的用户验证信息都存在于Cookie中，因此攻击者可以在不知道这些验证信息的情况下直接利用用户自己的Cookie来通过安全验证。由此可知，抵御CSRF攻击的关键在于：在请求中放入攻击者所不能伪造的信息，并且该信息不存在于Cookie之中。鉴于此，系统开发者可以在HTTP请求中以参数的形式加入一个随机产生的token，并在服务器端建立一个拦截器来验证这个token，如果请求中没有token或者token内容不正确，则认为可能是CSRF攻击而拒绝该请求。

这种方法要比检查 Referer 要安全一些，token 可以在用户登陆后产生并放于 session 之中，然后在每次请求时把 token 从 session 中拿出，与请求中的 token 进行比对，但这种方法的难点在于如何把 token 以参数的形式加入请求。对于 GET 请求，token 将附在请求地址之后，这样 URL 就变成 http://url?csrftoken=tokenvalue。 而对于 POST 请求来说，要在 form 的最后加上，这样就把 token 以参数的形式加入请求了。

## 3、 在HTTP头中自定义属性并验证
自定义属性的方法也是使用token并进行验证，和前一种方法不同的是，这里并不是把token以参数的形式置于HTTP请求之中，而是把它放到HTTP头中自定义的属性里。通过XMLHttpRequest这个类，可以一次性给所有该类请求加上csrftoken这个HTTP头属性，并把token值放入其中。这样解决了前一种方法在请求中加入token的不便，同时，通过这个类请求的地址不会被记录到浏览器的地址栏，也不用担心token会通过Referer泄露到其他网站。