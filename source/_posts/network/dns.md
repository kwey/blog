---
title: HTTP 状态消息
tags: 'brower'
categories: 'brower'
top_img: '/img/brower.jpeg'
---

以Chrome 浏览器为例：（访问： www.google.com


1、 Chrome 浏览器 会首先搜索浏览器自身的 DNS 缓存（缓存时间比较短，大概只有 1 分钟，且只能容纳 1000 条缓存），看自身的缓存中是否有 www.google.com 对应的条目，而且没有过期，如果有且没有过期则解析到此结束。


> 注：我们怎么查看Chrome自身的缓存？可以使用 <a href="chrome://net-internals/#dns" target="_blank">chrome://net-internals/#dns</a> 来进行查看


2、如果浏览器自身的缓存里面没有找到对应的条目，那么 Chrome 会搜索操作系统自身的 DNS 缓存,如果找到且没有过期则停止搜索解析到此结束.


>注：怎么查看操作系统自身的DNS缓存，以Windows系统为例，可以在命令行下使用 ipconfig /displaydns 来进行查看


3、如果在 Windows 系统的 DNS 缓存也没有找到，那么尝试读取 hosts 文件（位于 C:\\Windows\\System32\\drivers\\etc），看看这里面有没有该域名对应的 IP 地址，如果有则解析成功。


4、如果在 hosts 文件中也没有找到对应的条目，浏览器就会发起一个 DNS 的系统调用，就会向本地配置的首选 DNS 服务器发起域名解析请求。
（递归，通过的是UDP协议向DNS的53端口发起请求



4.1、运营商的DNS服务器首先查找自身的缓存，找到对应的条目，且没有过期，则解析成功


4.2、如果没有找到对应的条目，则有运营商的DNS代我们的浏览器发起迭代DNS解析请求


4.2.1、找根域的DNS的IP地址（这个DNS服务器都内置13台根域的DNS的IP地址），并发起请求，返回com域的IP地址


4.2.2、向com域的IP地址发起请求,返回google.com这个域的DNS地址


4.2.3、向google.com这个域名的DNS地址（这个一般就是由域名注册商提供的，像万网，新网等）发起请求，返回www.google.com这个域名对应的IP地址，并返回给Windows系统内核，内核又把结果返回给浏览器，终于浏览器拿到了www.google.com  对应的IP地址


一图胜千言：
![](/img/dns.jpg)



资料： 
<a href="https://www.ruanyifeng.com/blog/2016/06/dns.html" target="_blank">DNS 原理入门</a>

<a href="http://www.178linux.com/49376" target="_blank">DNS详解</a>