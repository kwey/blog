---
title: 浏览器的缓存机制
tags: 'brower'
categories: 'brower'
top_img: '/img/brower.jpeg'
---
> 浏览器的缓存机制也就是我们说的HTTP缓存机制，其机制是根据HTTP报文的缓存标识进行的

 ## 一：缓存规则解析
 ![](/img/cache1.png)
 ![](/img/cache2.png)
## 
1、强制缓存
主要有三种(暂不分析协商缓存过程)，如下：


* 不存在该缓存结果和缓存标识，强制缓存失效，则直接向服务器发起请求（跟第一次发起请求一致）


* 存在该缓存结果和缓存标识，但该结果已失效，强制缓存失效，则使用协商缓存(暂不分析)



* 存在该缓存结果和缓存标识，且该结果尚未失效，强制缓存生效，直接返回该结果
那么强制缓存的缓存规则是什么？


当浏览器向服务器发起请求时，服务器会将缓存规则放入HTTP响应报文的HTTP头中和请求结果一起返回给浏览器，
控制强制缓存的字段分别是Expires和Cache-Control，其中Cache-Control优先级比Expires高。

## Expires
Expires是HTTP/1.0控制网页缓存的字段，其值为服务器返回该请求结果缓存的到期时间，即再次发起该请求时，如果客户端的时间小于Expires的值时，直接使用缓存结果。


Expires是HTTP/1.0的字段，但是现在浏览器默认使用的是HTTP/1.1，那么在HTTP/1.1中网页缓存还是否由Expires控制？


到了HTTP/1.1，Expire已经被Cache-Control替代
原因在于Expires控制缓存的原理是使用客户端的时间与服务端返回的时间做对比，那么如果客户端与服务端的时间因为某些原因（例如时区不同；客户端和服务端有一方的时间不准确）发生误差，那么强制缓存则会直接失效，这样的话强制缓存的存在则毫无意义

## Cache-Control
Cache-Control的优先级比expires

在HTTP/1.1中，Cache-Control是最重要的规则，主要用于控制网页缓存，主要取值为：

<ul><li>
<span style="font-weight: bold; background-color: rgb(238, 236, 224); color: rgb(249, 150, 59);">public： 客户端和代理服务器都可缓存</li><li>

</li><li>
private：只有客户端可以缓存，默认取值</li><li>

</li><li>
no-cache：客户端缓存内容，但是是否使用缓存则需要经过协商缓存来验证决定</li><li>

</li><li>
no-store：所有内容都不会被缓存，即不使用强制缓存，也不使用协商缓存</li><li>

</li><li>
max-age=xxx (xxx is numeric)：缓存内容将在xxx秒后失效</li></ul>


浏览器的缓存存放在哪里，如何在浏览器中判断强制缓存是否生效？


1、from memory cache     代表使用内存中的缓存，
时效性：一旦该进程关闭，则该进程的内存则会清空。



2、from disk cache    代表使用的是硬盘中的缓存，


浏览器读取缓存的顺序为memory –> disk。


浏览器会在js和图片等文件解析执行后直接存入内存缓存中，那么当刷新页面时只需直接从内存缓存中读取(from memory cache)；


而css文件则会存入硬盘文件中，所以每次渲染页面都需要从硬盘读取缓存(from disk cache)。


## 二、对比缓存（协商缓存
协商缓存就是强制缓存失效后，浏览器携带缓存标识向服务器发起请求，由服务器根据缓存标识决定是否使用缓存的过程，主要有以下两种情况

 ![](/img/cache4.png)
同样，协商缓存的标识也是在响应报文的HTTP头中和请求结果一起返回给浏览器的，控制协商缓存的字段分别有：


Last-Modified / If-Modified-Since和Etag / If-None-Match，其中Etag / If-None-Match的优先级比Last-Modified / If-Modified-Since高。## 
## Last-Modified / If-Modified-Since
Last-Modified是服务器响应请求时，返回该资源文件在服务器最后被修改的时间，如下。 
![](/img/cache5.png)


If-Modified-Since则是客户端再次发起该请求时，携带上次请求返回的Last-Modified值，通过此字段值告诉服务器该资源上次请求返回的最后被修改时间。
服务器收到该请求，发现请求头含有If-Modified-Since字段，则会根据If-Modified-Since的字段值与该资源在服务器的最后被修改时间做对比，若服务器的资源最后被修改时间大于If-Modified-Since的字段值，则重新返回资源，状态码为200；否则则返回304，代表资源无更新，可继续使用缓存文件，如下。


 ![](/img/cache6.png)


## Etag / If-None-Match
Etag是服务器响应请求时，返回当前资源文件的一个唯一标识(由服务器生成)，如下。

 ![](/img/cache7.png)

If-None-Match是客户端再次发起该请求时，携带上次请求返回的唯一标识Etag值，通过此字段值告诉服务器该资源上次请求返回的唯一标识值。
服务器收到该请求后，发现该请求头中含有If-None-Match，则会根据If-None-Match的字段值与该资源在服务器的Etag值做对比，一致则返回304，代表资源无更新，继续使用缓存文件；不一致则重新返回资源文件，状态码为200，如下。

 ![](/img/cache8.png)

注：Etag / If-None-Match优先级高于Last-Modified / If-Modified-Since，同时存在则只有Etag / If-None-Match生效。

### 总结
强制缓存优先于协商缓存进行，若强制缓存(Expires和Cache-Control)生效则直接使用缓存，若不生效则进行协商缓存(Last-Modified / If-Modified-Since和Etag / If-None-Match)，协商缓存由服务器决定是否使用缓存，若协商缓存失效，那么代表该请求的缓存失效，重新获取请求结果，再存入浏览器缓存中；生效则返回304，继续使用缓存，主要过程如下：


 ![](/img/cache9.png)



协商缓存需要配合强缓存使用，你看前面这个截图中，除了Last-Modified这个header，还有强缓存的相关header，因为如果不启用强缓存的话，协商缓存根本没有意义。


## 浏览器行为对缓存的影响


如果资源已经被浏览器缓存下来，在缓存失效之前，再次请求时，默认会先检查是否命中强缓存，如果强缓存命中则直接读取缓存，如果强缓存没有命中则发请求到服务器检查是否命中协商缓存，如果协商缓存命中，则告诉浏览器还是可以从缓存读取，否则才从服务器返回最新的资源。


这是默认的处理方式，这个方式可能被浏览器的行为改变：


1）当ctrl+f5强制刷新网页时，直接从服务器加载，跳过强缓存和协商缓存；


2）当f5刷新网页时，跳过强缓存，但是会检查协商缓存；


参考文档：
<a id="cb_post_title_url" href="https://www.cnblogs.com/chenqf/p/6386163.html">彻底弄懂HTTP缓存机制及原理</a>
