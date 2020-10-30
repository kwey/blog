---
title: Http 206 文件断点续传下载原理
tags: 'http'
categories: 'brower'
top_img: '/img/brower.jpeg'
---
> 断点续传下载需要重视2对头信息Accept-Ranges/Range与If-Range/tag

## <a href="http://www.cnblogs.com/phpstudy2015-6/p/6821478.html" target="_blank">断点续传</a>

## 检测服务器端是否支持范围请求

假如在响应中存在 <a href="https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Accept-Ranges" title="服务器使用 HTTP 响应头 Accept-Range 标识自身支持范围请求(partial requests)。字段的具体值用于定义范围请求的单位。">Accept-Ranges</a> 首部（并且它的值不为 “none”），那么表示该服务器支持范围请求。例如，你可以使用 cURL 发送一个 <a href="https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/HEAD" title="HTTP HEAD 方法 请求资源的首部信息, 并且这些首部与 HTTP GET 方法请求时返回的一致. 该请求方法的一个使用场景是在下载一个大文件前先获取其大小再决定是否要下载, 以此可以节约带宽资源.">HEAD</a> 请求来进行检测。

``` javascript
curl -I http://i.imgur.com/z4d4kWk.jpg

HTTP/1.1 200 OK
...
Accept-Ranges: bytes
Content-Length: 146515
```

在上面的响应中， Accept-Ranges: bytes 表示界定范围的单位是 bytes 。这里  <a href="https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Length" title="Content-Length 是一个实体消息首部，用来指明发送给接收方的消息主体的大小，即用十进制数字表示的八位元组的数目。">Content-Length</a> 也是有效信息，因为它提供了要检索的图片的完整大小。

如果站点未发送 Accept-Ranges 首部，那么它们有可能不支持范围请求。一些站点会明确将其值设置为 "none"，以此来表明不支持。在这种情况下，某些应用的下载管理器会将暂停按钮禁用。
``` javascript
curl -I https://www.youtube.com/watch?v=EwTZ2xpQwpA

HTTP/1.1 200 OK
...
Accept-Ranges: none
```
## 从服务器端请求特定的范围
假如服务器支持范围请求的话，你可以使用 <a href="https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Range" title="The Range 是一个请求首部，告知服务器返回文件的哪一部分。在一个  Range 首部中，可以一次性请求多个部分，服务器会以 multipart 文件的形式将其返回。如果服务器返回的是范围响应，需要使用 206 Partial Content 状态码。假如所请求的范围不合法，那么服务器会返回  416 Range Not Satisfiable 状态码，表示客户端错误。服务器允许忽略  Range  首部，从而返回整个文件，状态码用 200 。">Range</a> 首部来生成该类请求。该首部指示服务器应该返回文件的哪一或哪几部分。

### 单一范围
我们可以请求资源的某一部分。这次我们依然用 cURL 来进行测试。"-H" 选项可以在请求中追加一个首部行，在这个例子中，是用 Range 首部来请求图片文件的前 1024 个字节。
``` javascript
curl http://i.imgur.com/z4d4kWk.jpg -i -H "Range: bytes=0-1023"
```

这样生成的请求如下：
``` javascript
GET /z4d4kWk.jpg HTTP/1.1
Host: i.imgur.com
Range: bytes=0-1023
```

服务器端会返回状态码为 <a href="https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/206" title="HTTP 206 Partial Content 成功状态响应代码表示请求已成功，并且主体包含所请求的数据区间，该数据区间是在请求的 Range 首部指定的。">206</a> Partial Content 的响应：
``` javascript
HTTP/1.1 206 Partial Content
Content-Range: bytes 0-1023/146515
Content-Length: 1024
...
(binary content)
```

在这里，<a href="https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Length" title="Content-Length 是一个实体消息首部，用来指明发送给接收方的消息主体的大小，即用十进制数字表示的八位元组的数目。">Content-Length</a> 首部现在用来表示先前请求范围的大小（而不是整张图片的大小）。<a href="https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Range" title="在HTTP协议中，响应首部 Content-Range 显示的是一个数据片段在整个文件中的位置。">Content-Range</a> 响应首部则表示这一部分内容在整个资源中所处的位置。<h3 id="多重范围">多重范围
Range头部也支持一次请求文档的多个部分。请求范围用一个逗号分隔开。
``` javascript
curl http://www.example.com -i -H "Range: bytes=0-50, 100-150"
```

服务器返回206 Partial Content状态码和Content-Type：multipart/byteranges; boundary=3d6b6a416f9b5头部，Content-Type：multipart/byteranges表示这个响应有多个byterange。每一部分byterange都有他自己的Centen-type头部和Content-Range，并且使用boundary参数对body进行划分。
``` javascript
HTTP/1.1 206 Partial Content
Content-Type: multipart/byteranges; boundary=3d6b6a416f9b5
Content-Length: 282

--3d6b6a416f9b5
Content-Type: text/html
Content-Range: bytes 0-50/1270

<!doctype html>
<html>
<head>
    <title>Example Do
--3d6b6a416f9b5
Content-Type: text/html
Content-Range: bytes 100-150/1270

eta http-equiv="Content-type" content="text/html; c
--3d6b6a416f9b5--
```
### 条件式范围请求
当（中断之后）重新开始请求更多资源片段的时候，必须确保自从上一个片段被接收之后该资源没有进行过修改。
The <a href="https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/If-Range" title="If-Range HTTP 请求头字段用来使得 Range 头字段在一定条件下起作用：当字段值中的条件得到满足时，Range 头字段才会起作用，同时服务器回复206 部分内容状态码，以及Range 头字段请求的相应部分；如果字段值中的条件没有得到满足，服务器将会返回 200 OK 状态码，并返回完整的请求资源。">If-Range</a> 请求首部可以用来生成条件式范围请求：假如条件满足的话，条件请求就会生效，服务器会返回状态码为 <a href="https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/206" title="HTTP 206 Partial Content 成功状态响应代码表示请求已成功，并且主体包含所请求的数据区间，该数据区间是在请求的 Range 首部指定的。">206</a> Partial 的响应，以及相应的消息主体。假如条件未能得到满足，那么就会返回状态码为 <a href="https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/200" title="状态码 200 OK 表明请求已经成功. 默认情况下状态码为200的响应可以被缓存。">200</a> OK 的响应，同时返回整个资源。该首部可以与  <a href="https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Last-Modified" title="The Last-Modified  是一个响应首部，其中包含源头服务器认定的资源做出修改的日期及时间。 它通常被用作一个验证器来判断接收到的或者存储的资源是否彼此一致。由于精确度比  ETag 要低，所以这是一个备用机制。包含有  If-Modified-Since 或 If-Unmodified-Since 首部的条件请求会使用这个字段。">Last-Modified</a> 验证器或者  <a href="https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/ETag" title="ETagHTTP响应头是资源的特定版本的标识符。这可以让缓存更高效，并节省带宽，因为如果内容没有改变，Web服务器不需要发送完整的响应。而如果内容发生了变化，使用ETag有助于防止资源的同时更新相互覆盖（“空中碰撞”）。">ETag</a> 一起使用，但是二者不能同时使用。
``` javascript
If-Range: Wed, 21 Oct 2015 07:28:00 GMT
```
## 范围请求的响应
与范围请求相关的有三种状态：<ul><li>在请求成功的情况下，服务器会返回  <a href="https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/206" title="HTTP 206 Partial Content 成功状态响应代码表示请求已成功，并且主体包含所请求的数据区间，该数据区间是在请求的 Range 首部指定的。">206</a> Partial Content 状态码。</li><li>在请求的范围越界的情况下（范围值超过了资源的大小），服务器会返回 <a href="https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/416" title="HTTP 416 Range Not Satisfiable 错误状态码意味着服务器无法处理所请求的数据区间。最常见的情况是所请求的数据区间不在文件范围之内，也就是说，Range 首部的值，虽然从语法上来说是没问题的，但是从语义上来说却没有意义。">416</a>Requested Range Not Satisfiable （请求的范围无法满足） 状态码。</li><li>在不支持范围请求的情况下，服务器会返回 <a href="https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/200" title="状态码 200 OK 表明请求已经成功. 默认情况下状态码为200的响应可以被缓存。">200</a> OK 状态码。</li></ul>

## 与分块传输编码的对比
<a href="https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Transfer-Encoding" title="Transfer-Encoding 消息首部指明了将 entity 安全传递给用户所采用的编码形式。">Transfer-Encoding</a> 首部允许分块编码，这在数据量很大，并且在请求未能完全处理完成之前无法知晓响应的体积大小的情况下非常有用。服务器会直接把数据发送给客户端而无需进行缓冲或确定响应的精确大小——后者会增加延迟。范围请求与分块传输是兼容的，可以单独或搭配使用。


