---
title: 通过rel="preload"进行内容预加载
tags: 'html'
categories: 'web'
top_img: '../../img/html.jpg'
---

> link 元素的 rel 属性的属性值preload能够让你在你的HTML页面中 head元素内部书写一些声明式的资源获取请求，可以指明哪些资源是在页面加载完成后即刻需要的

对于这种即刻需要的资源，你可能希望在页面加载的生命周期的早期阶段就开始获取，在浏览器的主渲染机制介入前就进行预加载。这一机制使得资源可以更早的得到加载并可用，且更不易阻塞页面的初步渲染，进而提升性能。本文提供了一个如何有效使用preload机制的基本说明。
## 基础部分
link 标签最常见的应用情形就是被用来加载CSS文件，进而装饰你的页面：
```html
<link rel="stylesheet" href="styles/main.css">
```
但是在这里，我们将使用preload作为rel属性的属性值。这种做法将把<link> 元素塞入一个预加载器中，这个预加载器也将用于其他我们所需要的，各种各样的，任意类型的资源。为了完成基本的配置，你还需要通过 <a href="https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/link#attr-href">href</a>和<a href="https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/link#attr-as">as</a> 属性指定需要被预加载资源的资源路径及其类型。
一个简单的例子可能看起来像下面这样 （在这里可以查看示例的<a rel="noopener" href="https://github.com/mdn/html-examples/tree/master/link-rel-preload/js-and-css">JS和CSS源代码</a>，或是<a rel="noopener" href="https://mdn.github.io/html-examples/link-rel-preload/js-and-css/">在线实例</a>）
```html
<head>
    <meta charset="utf-8">
    <title>JS and CSS preload example</title>
    <link rel="preload" href="style.css" as="style">
    <link rel="preload" href="main.js" as="script">
    <link rel="stylesheet" href="style.css">
</head>

<body>
    <h1>bouncing balls</h1>
    <canvas></canvas>
  <script src="main.js"></script>
</body>
```
在这里，我们预加载了CSS和JavaScript文件，所以在随后的页面渲染中，一旦需要使用它们，它们就会立即可用。这个例子本身可能有些平淡，但预加载的好处可以更清晰直观的得到展示，在随后的渲染过程中，这些资源得到有效使用。对于更大的文件来说，也是如此。 例如那些在CSS文件中指向的资源，比如字体或是图片；再比如更大的图片和视频文件。

preload 还有许多其他好处。使用 as 来指定将要预加载的内容的类型，将使得浏览器能够：
* 更精确地优化资源加载优先级。
* 匹配未来的加载需求，在适当的情况下，重复利用同一资源。
为资源应用正确的<a href="https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP">内容安全策略</a>。
为资源设置正确的 <a href="https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Accept">Accept</a> 请求头。

## 哪些类型的内容可以被预加载？

许多不同类型的内容都可以被预加载，一些主要可用的 as 属性值列举如下：
* audio: 音频文件。
* document: 一个将要被嵌入到<a href="https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/frame">frame</a>或<a href="https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/iframe" >iframe</a>内部的HTML文档。
* embed: 一个将要被嵌入到<a href="https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/embed" title="HTML <embed> 元素将外部内容嵌入文档中的指定位置。此内容由外部应用程序或其他交互式内容源（如浏览器插件）提供。"><embed></a>元素内部的资源。
* fetch: 那些将要通过fetch和XHR请求来获取的资源，比如一个ArrayBuffer或JSON文件。
* font: 字体文件。
* image: 图片文件。
* object: 一个将会被嵌入到<a href="https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/embed" title="HTML <embed> 元素将外部内容嵌入文档中的指定位置。此内容由外部应用程序或其他交互式内容源（如浏览器插件）提供。"><embed></a>元素内的文件。
* script: JavaScript文件。
* style: 样式表。
* track: WebVTT文件。
* worker: 一个JavaScript的web worker或shared worker。
* video: 视频文件。

注意: 你可以通过进一步阅读<a rel="noopener" href="https://w3c.github.io/preload/#link-element-extensions">link element extensions</a>来了解关于这些属性值以及其他在Preload方案中预期将采纳的特性的细节。同样需要注意的是，关于as属性的有效值得完整列表是由Fetch方案来制定的，可以查看<a rel="noopener" href="https://fetch.spec.whatwg.org/#concept-request-destination">request destinations</a>来进行了解。

## 包含一个MIME类型
link 元素可以接受一个<a href="https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/link#attr-type">type</a>属性。这一属性可以包含该元素所 指向资源的MIME类型。在浏览器进行 预加载的时候，这个属性值将会 非常有用——浏览器将使用type属性来判断它是否支持这一资源，如果浏览器支持这一类型资源的预加载，下载将会开始，否则便对其加以忽略。

你可以在我们的视频示例中看到一个与此有关的示例（<a href="https://github.com/mdn/html-examples/tree/master/link-rel-preload/video" target="_blank">查看完整源码</a>，也可以<a href="https://mdn.github.io/html-examples/link-rel-preload/js-and-css/" target="_blank">查看在线示例</a>）:
```html
<head>
  <meta charset="utf-8">
  <title>Video preload example</title>

  <link rel="preload" href="sintel-short.mp4" as="video" type="video/mp4">
</head>
<body>
  <video controls>
    <source src="sintel-short.mp4" type="video/mp4">
    <source src="sintel-short.webm" type="video/webm">
    <p>Your browser doesn't support HTML5 video. Here is a <a href="sintel-short.mp4">link to the video</a> instead.</p>
  </video>
</body>
```
在这个实例中，支持MP4格式的浏览器将仅预加载并使用MP4资源，以使得视频播放器的表现尽可能的流畅，或者说，为用户提供更好的响应。而不支持MP4格式的浏览器仍然能够加载视频的WebM版本，但无法体验到预加载带来的良好体验。这个例子展示了预加载机制如何与渐进式增强的哲学进行有机的结合。

## 跨域获取
如果你已经有了一个可以正确工作的<a href="https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS">CORS</a>设置，那么你也可以同样成功的预加载那些跨域资源，只需要你在<link>元素中设置好<a href="https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/link#attr-crossorigin">crossorigin</a>属性即可。

一个有趣的情况是，如果你需要获取的是字体文件，那么即使是非跨域的情况下，也需要应用这一属性。因为各种各样的原因，这些获取请求必须使用以匿名模式使用CORS（如果你对其中的细节感兴趣，可以查看<a href="https://drafts.csswg.org/css-fonts/#font-fetching-requirements" target="_blank">Font fetching requirements</a>一文）。

我们将以这个情况作为一个示例——首先是由于字体文件的加载是预加载方面一个好的用例，其次，这也比真正的配置一个跨域请求的例子要简单许多。你可以查看 <a href="https://github.com/mdn/html-examples/tree/master/link-rel-preload/fonts" target="_blank">在Github上的示例源代码</a>（也可以查看<a href="https://mdn.github.io/html-examples/link-rel-preload/fonts/" target="_blank">在线示例</a>）：
```html
<head>
  <meta charset="utf-8">
  <title>Web font example</title>

  <link rel="preload"href="fonts/cicle_fina-webfont.eot" as="font" type="application/vnd.ms-fontobject" crossorigin="anonymous">
  <link rel="preload" href="fonts/cicle_fina-webfont.woff2" as="font" type="font/woff2" crossorigin="anonymous">
  <link rel="preload" href="fonts/cicle_fina-webfont.woff" as="font" type="font/woff" crossorigin="anonymous">
  <link rel="preload" href="fonts/cicle_fina-webfont.ttf" as="font" type="font/ttf" crossorigin="anonymous">
  <link rel="preload" href="fonts/cicle_fina-webfont.svg" as="font" type="image/svg+xml" crossorigin="anonymous">

  <link rel="preload" href="fonts/zantroke-webfont.eot" as="font" type="application/vnd.ms-fontobject" crossorigin="anonymous">
  <link rel="preload" href="fonts/zantroke-webfont.woff2" as="font" type="font/woff2" crossorigin="anonymous">
  <link rel="preload" href="fonts/zantroke-webfont.woff" as="font" type="font/woff" crossorigin="anonymous">
  <link rel="preload" href="fonts/zantroke-webfont.ttf" as="font" type="font/ttf" crossorigin="anonymous">
  <link rel="preload" href="fonts/zantroke-webfont.svg" as="font" type="image/svg+xml" crossorigin="anonymous">

  <link href="style.css" rel="stylesheet" type="text/css">
</head>
<body>
  ...
</body>
```
你可以看到，在这里，我们不仅通过配置type属性提供了一个MIME类型的暗示，我们也提供了一个crossorigin 属性来处理CORS问题。

## 包含媒体
<link>元素有一个很棒的特性是它们能够接受一个<a href="https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/link#attr-media">media</a>属性。它们可以接受<a href="https://developer.mozilla.org/en-US/docs/Web/CSS/@media#Media_types">媒体类型</a>或有效的<a href="https://developer.mozilla.org/en-US/docs/Web/CSS/Media_Queries/Using_media_queries">媒体查询</a>作为属性值，这将令你能够使用响应式的预加载！
让我们来看一个简单的示例（可以查看<a href="https://github.com/mdn/html-examples/tree/master/link-rel-preload/media" target="_blank">Github上的源代码</a>或<a href="https://mdn.github.io/html-examples/link-rel-preload/media/" target="_blank">在线示例</a>）：

```html
<head>
  <meta charset="utf-8">
  <title>Responsive preload example</title>

  <link rel="preload" href="bg-image-narrow.png" as="image" media\"(max-width: 600px)">
  <link rel="preload" href="bg-image-wide.png" as="image" media="(min-width: 601px)">

  <link rel="stylesheet" href="main.css">
</head>
<body>
  <header>
    <h1>My site</h1>
  </header>

  <script>
    var mediaQueryList = window.matchMedia("(max-width: 600px)");
    var header = document.querySelector('header');

    if(mediaQueryList.matches) {
          header.style.backgroundImage = 'url(bg-image-narrow.png)';
    } else {
          header.style.backgroundImage = 'url(bg-image-wide.png)';
    }
  </script>
</body>
```
你可以看到我们在link元素中包含了一个media属性，因此，当用户在使用较窄屏幕的设备时，较窄的图片将会被预加载，而在较宽的设备上，较宽的图片将被预加载。然后我们仍需要在header元素上附加合适的图片——通过<a href="https://developer.mozilla.org/zh-CN/docs/Web/API/Window/matchMedia" title="返回一个新的MediaQueryList 对象，表示指定的媒体查询字符串解析后的结果。">Window.matchMedia</a> / <a href="https://developer.mozilla.org/zh-CN/docs/Web/API/MediaQueryList" title="一个MediaQueryList对象在一个document上维持着一系列的媒体查询，并负责处理当媒体查询在其document上发生变化时向监听器进行通知的发送。">MediaQueryList</a> 来加以实现（可以查看<a href="https://developer.mozilla.org/en-US/docs/Web/CSS/Media_Queries/Testing_media_queries">Testing media queries</a>一文来了解更多信息）。
这一特性将使另一种情况成为可能——字体在随着页面渲染完成的时候即可使用，减少了FOUT (无样式字体闪烁，flash of unstyled text)问题。
值得注意的是，这一特特性并不仅限于图片，或其他有着同样类型的文件，还有更多想象空间。比如，你可以在用户仅有较窄的屏幕，CPU和带宽资源较为有限的情况下预加载并展示一个简单的SVG图表，而在用户资源较为充裕的时候再去加载一系列复杂的JavaScript文件以显示一个有交互功能的3D模型。

## 脚本化与预加载
另一项很棒的关于预加载的事情是，如果需要，你可以完全以脚本化的方式来执行这些预加载操作。例如，我们在这里创建一个<a href="https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLLinkElement" target="_blank">HTMLLinkElement</a>实例，然后将他们附加到DOM上

```javascript
var preloadLink = document.createElement("link");
preloadLink.href = "myscript.js";
preloadLink.rel = "preload";
preloadLink.as = "script";
document.head.appendChild(preloadLink);
```
这意味着浏览器将预加载这个JavaScript文件，但并不实际执行它。
如果要对其加以执行，在需要的时候，你可以执行：
```javascript
var preloadedScript = document.createElement("script");
preloadedScript.src = "myscript.js";
document.body.appendChild(preloadedScript);
```
当你需要预加载一个脚本，但需要推迟到需要的时候才令其执行时，这种方式会特别有用。

## 其他资源预加载机制
还存在一些其他预加载机制，但没有哪个会比`<link rel="preload">`在大多数情况下更符合你的需要和预期：
`<link rel="prefetch">` 已经被许多浏览器支持了相当长的时间，但它是意图预获取一些资源，以备下一个导航/页面使用（比如，当你去到下一个页面时）。这很好，但对当前的页面并没有什么助益。此外，浏览器会给使用prefetch的资源一个相对较低的优先级——与使用preload的资源相比。毕竟，当前的页面比下一个页面相对更加重要。查看<a href="https://developer.mozilla.org/en-US/docs/Web/HTTP/Link_prefetching_FAQ">Link prefetching FAQ</a>可以了解更多细节。

`<link rel="subresource">`被Chrome支持了有一段时间，并且已经有些搔到预加载当前导航/页面（所含有的资源）的痒处了。但它有一个问题——没有办法处理所获取内容的优先级（as也并不存在），所以最终，这些资源会以一个相当低的优先级被加载，这使得它能提供的帮助相当有限。

除以上这些意外，还有大量的基于脚本的资源加载器。但这些加载器对于浏览器的加载优先级队列完全束手无策，这也使得他们不得不屈服于同样的性能问题。


<a href="https://developer.mozilla.org/zh-CN/docs/Web/HTML/Preloading_content" target="_blank" style="font-size: x-large;">MDN</a><br>
