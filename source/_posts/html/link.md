---
title: HTML 中<link>元素
tags: 'html'
categories: 'web'
top_img: '../../img/html.jpg'
---

> HTML 中link元素规定了外部资源与当前文档的关系。 这个元素可用来为导航定义一个关系框架。这个元素最常于链接样式表。

<table><tbody><tr><th><a href="https://developer.mozilla.org/en-US/docs/Web/HTML/Content_categories">内容类型</a></th><td>元数据。如果使用了 <a href="https://developer.mozilla.org/zh-CN/docs/Web/HTML/Global_attributes/itemprop">itemprop</a> 属性, 则为 <a href="https://developer.mozilla.org/en-US/docs/Web/HTML/Content_categories#Flow_content">flow content</a> 和 <a href="https://developer.mozilla.org/en-US/docs/Web/HTML/Content_categories#Phrasing_content">phrasing content</a>.</td></tr><tr><th>允许的元素内容</th><td>无，这是一个<a href="https://developer.mozilla.org/zh-CN/docs/Glossary/%E7%A9%BA%E5%85%83%E7%B4%A0" title="The definition of that term (空元素) has not been written yet; please consider contributing it!">空元素</a>。</td></tr><tr><th>标签省略</th><td>鉴于这是一个空元素，开始标签必须存在，结束标签必须不存在。</td></tr><tr><th>允许的父元素</th><td>任何可以接受元数据的元素.。如果使用了 <a href="https://developer.mozilla.org/zh-CN/docs/Web/HTML/Global_attributes/itemprop">itemprop</a>属性,，则其父元素可以是任何可接受 <a href="https://developer.mozilla.org/en-US/docs/Web/HTML/Content_categories#Phrasing_content">phrasing content</a> 的元素。</td></tr><tr><th>DOM接口</th><td><a href="https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLLinkElement" title="The HTMLLinkElement interface represents reference information for external resources and the relationship of those resources to a document and vice-versa. This object inherits all of the properties and methods of the HTMLElement interface.">HTMLLinkElement</a></td></tr></tbody></table>

## 属性
这个元素可以使用 <a href="https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes">全局属性</a>

### as

该属性仅在 link 元素设置了 rel="preload" 时才能使用。

它规定了 link 元素加载的内容的类型，对于内容的优先级、请求匹配、正确的[内容安全策略](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/CSP)的选择以及正确的 [Accept](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Accept) 请求头的设置，这个属性是必需的。

### crossorigin

此枚举属性指定在加载相关图片时是否必须使用 CORS.[ 启用 CORS 的图片](https://developer.mozilla.org/en-US/docs/Web/HTML/CORS_Enabled_Image) 可以在 [canvas](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/canvas) 元素中使用, 并避免其被污染. 

可取的值如下:

    "anonymous"

    会发起一个跨域请求(即包含 Origin: HTTP 头). 但不会发送任何认证信息 (即不发送 cookie, X.509 证书和 HTTP 基本认证信息). 如果服务器没有给出源站凭证 (不设置 Access-Control-Allow-Origin: HTTP 头), 这张图片就会被`污染并限制使用`.

     "use-credentials"

    会发起一个带有认证信息 (发送 cookie, X.509 证书和 HTTP 基本认证信息) 的跨域请求 (即包含 Origin: HTTP 头). 如果服务器没有给出源站凭证 (不设置 Access-Control-Allow-Origin: HTTP 头), 这张图片就会被`污染并限制使用`.

当不设置此属性时, 资源将会不使用 CORS 加载 (即不发送 Origin: HTTP 头), 这将阻止其在 <a href="https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/canvas" >canvas</a> 元素中进行使用. 若设置了非法的值, 则视为使用 anonymous. 前往 <a href="https://developer.mozilla.org/en-US/docs/Web/HTML/CORS_settings_attributes">CORS settings attributes</a> 获取更多信息.

### disabled 

此属性被用于禁用链接关系. 配合脚本使用, 该属性可以用来启用或禁用多个样式表链接.

>使用注意: 虽然在 HTML 标准中没有 disabled 属性, 但在 HTMLLinkElement DOM 对象中确实存在 disabled 属性.

使用 disabled 作为 HTML 属性是非标准的, 并且只有部分浏览器使用 (<a rel="noopener" href="https://www.w3.org/Bugs/Public/show_bug.cgi?id=27677">W3 #27677</a>)。

所以不要使用它。要达到类似的效果, 可以选用以下方法:
> 如果 disabled 属性已经直接加入到页面元素中, 可以改为不引入这个这个 link元素

> 通过脚本为该样式表 DOM 对象设置 disabled 属性.
       

### [href](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/link)

### hreflang
此属性指明了被链接资源的语言. 其意义仅供参考。可取的值参见 <a rel="noopener" href="http://www.ietf.org/rfc/bcp/bcp47.txt">BCP47</a>。仅当设置了 <a href="https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/a#attr-href">href</a> 属性时才应设置该属性。

### integrity
包含行内元数据，它是一个你用浏览器获取的资源文件的哈希值，以base64编码的方式加的密，这样用户能用它来验证一个获取到的资源,在传送时未被非法篡改，详情查看<a href="https://developer.mozilla.org/en-US/docs/Web/Security/Subresource_Integrity">Subresource Integrity</a>。

### media
这个属性规定了外部资源适用的媒体类型。它的值必须是"<a href="https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Media_queries">媒体查询</a>"。这个属性使得用户代理能选择最适合设备运行的媒体类型。

>使用注意：
在HTML 4中，该属性只能是一组以空白符作为分隔的媒体描述文字，比如"<a href="https://developer.mozilla.org/zh-CN/docs/Web/CSS/@media">媒体类型</a>"规定了该元素可取的属性，如print、screen、aural、braille。HTML5将该属性值扩展为任意类型的"<a rel="nofollow" href="https://developer.mozilla.org/zh-CN/docs/CSS/Media_queries">媒体查询</a>"，"媒体查询"将HTML4的属性值都包括在内。
不支持"<a rel="nofollow" href="https://developer.mozilla.org/zh-CN/docs/CSS/Media_queries">CSS3 媒体查询</a>"的浏览器并不会强行识别这些链接，因此忘了设置备用link，即那些可用于HTML4的link。

### rel
此属性指明被链接文档对于当前文档的关系。这个属性一定得是一个由空格分开的<a href="https://developer.mozilla.org/zh-CN/docs/Web/HTML/Link_types">链接类型值</a>的列表。 这个属性最通常的用法是指向一个连接到外部样式表的链接：将rel的值设置为stylesheet，href属性设置为外部样式表的URL来构造网页。网络电视还支持使用下一个rel的值在一系列页面中预加载下一个页面。

### sizes
这个属性定义了包含相应资源的可视化媒体中的icons的大小。它只有在<a href="https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/link#attr-rel">rel</a>包含icon的<a href="https://developer.mozilla.org/zh-CN/docs/Web/HTML/Link_types">link类型值</a>。它可能有如下的规则。

any，意味着这个icon能够被缩放到任意尺寸当它是矢量形式，比如image/svg+xml。
一个由空白符分隔的尺寸列表。每一个都以x 或 X给出。尺寸列表中的每一个尺寸都必须包含在资源里。

>用法注意：

大多数的icon格式只能存储一个icon。因此绝大多数使用 <a href="https://developer.mozilla.org/zh-CN/docs/Web/HTML/Global_attributes#attr-sizes">sizes</a>时只包含一个值。微软的ICO格式和苹果的ICNS格式都是这样，ICO使用得更加广泛，推荐你使用它。
苹果的IOS系统并不支持这个属性，于是苹果的IPhone以及IPad使用特殊的、非标准的 <a href="https://developer.mozilla.org/zh-CN/docs/Web/HTML/Link_types">link类型值</a>去定义作为Web Clip或开始占位符：apple-touch-icon 和 apple-touch-startup-icon。
### target
Defines the frame or window name that has the defined linking relationship or that will show the rendering of any linked resource.
### type
这个属性被用于定义链接的内容的类型。这个属性的值应该是像text/html，text/css等MIME类型。这个属性常用的用法是定义链接的样式表，最常用的值是表明了CSS的text/css。
<a href="https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/link#%E6%A0%B7%E5%BC%8F%E8%A1%A8%E5%8A%A0%E8%BD%BD%E4%BA%8B%E4%BB%B6">样式表加载事件</a><a href="https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/link#%E6%A0%B7%E5%BC%8F%E8%A1%A8%E5%8A%A0%E8%BD%BD%E4%BA%8B%E4%BB%B6"></a>能够通过监听发生在样式表上的事件知道什么时候样式表加载完毕。同样的，你能够通过监听error事件检测到是否在加载样式表的过程中出现错误。

``` html
<p>
    <script>
        function sheetLoaded() {
            // Do something interesting; the sheet has been loaded
        }

        function sheetError() {
            alert("An error occurred loading the stylesheet!");
        }
    </script>
</p>
<link rel="stylesheet" href="mystylesheet.css" onload="sheetLoaded()" onerror="sheetError()">
```
>注意
link 标签只能出现在head元素中，然而可以出现多个link标签。
HTML 3.2只为link元素定义了href, methods, rel，rev，title，和urn属性。
HTML 2为link标签定义了 href, methods，rel，rev，title，和 urn 属性，methods 和 urn随后从规范中移除。
HTML和XHTML规范为link定义了事件处理，但是并不清楚它们将会怎样被使用。
在XHTML 1.0中，空元素link要求有尾随斜线，像这样link。


[MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/link)
