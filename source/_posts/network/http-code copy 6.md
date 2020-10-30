---
title: HTTP的神秘面纱
tags: 'http'
categories: 'brower'
top_img: '/img/brower.jpeg'
---
> OSI（Open System Interconnect），即开放式系统互联。 一般都叫OSI参考模型，是ISO（国际标准化组织）组织在1985年研究的网络互连模型。

![](/img/http0.png)
## 各层的作用
![](/img/http1.png)

1.物理层：

主要定义物理设备标准，如网线的接口类型、光纤的接口类型、各种传输介质的传输速率等。它的主要作用是传输比特流（就是由1、0转化为电流强弱来进行传输,到达目的地后在转化为1、0，也就是我们常说的数模转换与模数转换）。这一层的数据叫做比特。 

　　
2.数据链路层：

定义了如何让格式化数据以进行传输，以及如何让控制对物理介质的访问。这一层通常还提供错误检测和纠正，以确保数据的可靠传输。 　
　

3.网络层：

在位于不同地理位置的网络中的两个主机系统之间提供连接和路径选择。Internet的发展使得从世界各站点访问信息的用户数大大增加，而网络层正是管理这种连接的层。

　　
4.传输层：

定义了一些传输数据的协议和端口号（WWW端口80等），如： 

TCP（transmission control protocol –传输控制协议，传输效率低，可靠性强，用于传输可靠性要求高，数据量大的数据） 
UDP（user datagram protocol–用户数据报协议，与TCP特性恰恰相反，用于传输可靠性要求不高，数据量小的数据，如QQ聊天数据就是通过这种方式传输的）。 主要是将从下层接收的数据进行分段和传输，到达目的地址后再进行重组。常常把这一层数据叫做段。 


5.会话层：

通过运输层（端口号：传输端口与接收端口）建立数据传输的通路。主要在你的系统之间发起会话或者接受会话请求（设备之间需要互相认识可以是IP也可以是MAC或者是主机名


6.表示层

可确保一个系统的应用层所发送的信息可以被另一个系统的应用层读取。例如，PC程序与另一台计算机进行通信，其中一台计算机使用扩展二一十进制交换码（EBCDIC），而另一台则使用美国信息交换标准码（ASCII）来表示相同的字符。如有必要，表示层会通过使用一种通格式来实现多种数据格式之间的转换


7.应用层：

是最靠近用户的OSI层。这一层为用户的应用程序（例如电子邮件、文件传输和终端仿真）提供网络服务

## HTTP 发展历史HTTP/0.9

<ul><li>只有一个命令GET</li><li>响应类型: 仅 超文本</li><li>没有header等描述数据的信息</li><li>服务器发送完毕，就关闭TCP连接</li><li>
</li></ul>HTTP/1.0<ul><li>增加了很多命令（post HESD ）</li><li>增加status code 和 header</li><li>多字符集支持、多部分发送、权限、缓存等</li><li>响应：不再只限于超文本 (Content-Type 头部提供了传输 HTML 之外文件的能力 — 如脚本、样式或媒体文件)</li><li>
</li></ul>HTTP/1.1<ul><li>持久连接。TCP三次握手会在任何连接被建立之前发生一次。最终，当发送了所有数据之后，服务器发送一个消息，表示不会再有更多数据向客户端发送了；则客户端才会关闭连接（断开 TCP）</li><li>
</li><li>支持的方法: GET , HEAD , POST , PUT , DELETE , TRACE , OPTIONS</li><li>进行了重大的性能优化和特性增强，分块传输、压缩/解压、内容缓存磋商、虚拟主机（有单个IP地址的主机具有多个域名）、更快的响应，以及通过增加缓存节省了更多的带宽</li><li>
</li></ul>HTTP2<ul><li>所有数据以二进制传输。HTTP1.x是基于文本的，无法保证健壮性，HTTP2.0绝对使用新的二进制格式，方便且健壮</li><li>同一个连接里面发送多个请求不再需要按照顺序来</li><li>头信息压缩以及推送等提高效率的功能</li><li>
</li></ul>URI、URL、URNURI: Uniform Resource Identifier/统一资源标识符
URL: Uniform Resource Locator/统一资源定位器
URN: Uniform Resource Name/永久统一资源定位符
web上的各种资源（html、图片、视频、音频等）都由一个URI标识定位。URI相当于它们的详细“家庭住址”。
URI包含了URL和URN。

![](/img/http3.jpg)



URL是URI的一种，不仅标识了Web 资源，还指定了操作或者获取方式，同时指出了主要访问机制和网络位置。


URN是URI的一种，用特定命名空间的名字标识资源。使用URN可以在不知道其网络位置及访问方式的情况下讨论资源。


```
// 这是一个URI
http://bitpoetry.io/posts/hello.html#intro

// 资源访问方式
http://

// 资源存储位置
bitpoetry.io/posts/hello.html

#intro // 资源

// URL
http://bitpoetry.io/posts/hello.html

// URN
bitpoetry.io/posts/hello.html#intro
```

#HTTP报文
请求报文：
![](/img/http4.jpg)

响应报文：
![](/img/http5.jpg)


## HTTP 各种特性## curl


curl命令是一个利用URL规则在命令行下工作的文件传输工具。它支持文件的上传和下载，所以是综合传输工具，但按传统，习惯称curl为下载工具。作为一款强力工具，curl支持包括HTTP、HTTPS、ftp等众多协议，还支持POST、cookies、认证、从指定偏移处下载部分文件、用户代理字符串、限速、文件大小、进度条等特征。做网页处理流程和数据检索自动化，curl可以祝一臂之力。

<a href="http://man.linuxde.net/curl" target="_blank">curl命令</a>


## CORS跨域请求的限制与解决
``` html
<!-- 前端： -->
<script>
    fetch(url,{
        method: 'post',
        headers: {
            'X-Test-Cors': '123'
        }
    });
</script>
<!-- 服务端要加个请求头 -->
```
``` javascript
response.writeHead(200, {
// 加上这个设置
    'Access-Control-Allow-Headers': 'X-Test-Cors'
})
``` 
     

## 缓存Cache-Control

<table><thead><tr><th>Cache-Control</th><th>说明</th></tr></thead><tbody><tr><td>public</td><td>所有内容都将被缓存(客户端和代理服务器都可缓存)</td></tr><tr><td>private</td><td>内容只缓存到私有缓存中(仅客户端可以缓存，代理服务器不可缓存)</td></tr><tr><td>no-cache</td><td>必须先与服务器确认返回的响应是否被更改，然后才能使用该响应来满足后续对同一个网址的请求。因此，如果存在合适的验证令牌 (ETag)，no-cache 会发起往返通信来验证缓存的响应，如果资源未被更改，可以避免下载。</td></tr><tr><td>no-store</td><td>所有内容都不会被缓存到缓存或 Internet 临时文件中</td></tr><tr><td>must-revalidation/proxy-revalidation</td><td>如果缓存的内容失效，请求必须发送到服务器/代理以进行重新验证</td></tr><tr><td>max-age=xxx (xxx is numeric)</td><td>缓存的内容将在 xxx 秒后失效, 这个选项只在HTTP 1.1可用, 并如果和Last-Modified一起使用时, 优先级较高</td></tr></tbody></table>


``` javascript
// 服务端设置响应头
response.writeHead(200, {
        'Content-Type': 'text/javascript',
    
// 缓存20s 多个值用逗号分开  'Cache-Control': 'max-age=20,public' 
})
response.end('console.log("script loaded")')
```

    
    
参考文献：
<a href="https://finget.github.io/2018/07/03/http/" target="_blank">前端工程师，揭开HTTP的神秘面纱</a>

<a href="http://www.ruanyifeng.com/blog/2012/05/internet_protocol_suite_part_i.html" target="_blank">互联网协议入门（一）</a>

<a href="http://www.ruanyifeng.com/blog/2012/06/internet_protocol_suite_part_ii.html" target="_blank">互联网协议入门（二）</a>

<a href="https://zhuanlan.zhihu.com/p/45136487" target="_blank">HTTP协议基础及发展历史</a>"