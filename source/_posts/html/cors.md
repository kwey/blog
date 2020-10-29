---
title: CORS settings attributes
tags: 'html'
categories: 'web'
top_img: '/img/html.jpg'
---
> 在HTML5中，一些 HTML 元素提供了对 CORS 的支持， 例如`<img>和 <video>`均有一个跨域属性 (crossOriginproperty)，它允许你配置元素获取数据的 CORS 请求。


默认情况下 （即未指定crossOrigin属性时）, CORS 根本不会使用。
如<a href="https://www.w3.org/TR/cors/#user-credentials" target="_blank">Terminology section of the CORS specification</a>中的描述，“anonymous" 关键字说明不会通过 cookies，客户端 SSL 证书或 HTTP 认证交换用户凭据。
即使是无效的关键字和空字符串也会被当作anonymous关键字使用。

## 示例: 使用 crossorigin 的 script 元素

你可以使用下面的<a href="https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/script" target="_blank" rel="nofollow">`<script>`</a>元素告诉一个浏览器执行来自https://example.com/example-framework.js的脚本而不发送用户凭据。

```html
<script src="https://example.com/example-framework.js" crossorigin="anonymous"></script>
```
## “被污染”的 canvas
<a href="https://developer.mozilla.org/zh-CN/docs/Web/HTML/CORS_enabled_image" target="_blank">MDN</a>

尽管不通过 CORS 就可以在画布中使用图片，但是这会污染画布。一旦画布被污染，你就无法读取其数据。例如，你不能再使用画布的toBlob(),toDataURL()或getImageData()方法，调用它们会抛出安全错误。
这种机制可以避免未经许可拉取远程网站信息而导致的用户隐私泄露。

## 示例: 存储一张外部域中的图片

你必须有一个可以对图片响应正确Access-Control-Allow-Origin响应头的服务器。你可以使用以下片段 (来自<a rel="noopener" href="https://github.com/h5bp/server-configs-apache/blob/fc379c45f52a09dd41279dbf4e60ae281110a5b0/src/.htaccess#L36-L53">HTML5 Boilerplate Apache server configs</a>) 实现正确响应头。

```html
<IfModule mod_setenvif.c>
  <IfModule mod_headers.c>
    <FilesMatch "\\.(cur|gif|ico|jpe?g|png|svgz?|webp)$">
      SetEnvIf Origin ":" IS_CORS
      Header set 
        Access-Control-Allow-Origin "*" env=IS_CORS
    </FilesMatch>
  </IfModule>
</IfModule><span aria-hidden="true"></span>
```

配置完毕后，你就可以将这些图片保存到<a href="https://developer.mozilla.org/zh-CN/docs/Web/Guide/API/DOM/Storage" title="/zh-CN/docs/Web/Guide/API/DOM/Storage">DOM 存储</a>中了，就像这些图片在你自己域名之下一样。

```javascript
var img = new Image,
    canvas = document.createElement("canvas"),
    ctx = canvas.getContext("2d"),
    src = "http://example.com/image"; 
    // insert image url here

img.crossOrigin = "Anonymous";

img.onload = function() {
    canvas.width = img.width;
    canvas.height = img.height;
    ctx.drawImage( img, 0, 0 );
    localStorage.setItem(
        "savedImageData",
        canvas.toDataURL("image/png")
    );
}
img.src = src;
// make sure the load event fires for cached images too
if ( img.complete || img.complete === undefined ) {
    img.src = "data:image/
    gif;base64,R0lGODlhAQABAIAAAAAAAP
    ...ywAAAAAAQABAAACAUwAOw==";
    img.src = src;
}
```








