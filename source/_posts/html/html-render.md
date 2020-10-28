---
title: CSS 与 JS 阻塞 DOM 解析和渲染的
tags: 'html'
categories: 'web'
top_img: '../../img/html.jpg'
---

link 标签放在头部性能会高一点，少一点人知道如果 script 与 link 同时在头部的话， script 在上可能会更好
## CSS不会阻塞 DOM 的解析

``` html
<!DOCTYPE html>
    <html lang="en">
    <head>
      <meta charset="UTF-8">
      <style>
       div {
            width: 100px;
            height: 100px;
            background: lightgreen;
       }
      </style>
    </head>
    <body>
        <div></div>
    </body>
    </html>

```
> 注意哦！这里说的是 DOM 解析，证明的例子如下，首先在头部插入
``` html
     <script defer src="/*.js"></script>，
```
JS 文件的内容是：
``` javascript
    const div = document.querySelecotor('div');
    console.log(div);  
```


* defer : 用来通知浏览器该脚本将在文档完成解析后，触发 DOMContentLoaded 事件前执行。设置这个属性，能保证 DOM 解析后马上打印出 div。

之后将
``` html
<link rel="stylesheet" href="3s.css">
<!-- (文件下载要3s) -->
```
插入 HTML 文件的任一位置，打开浏览器，可以看到是首先打印出 div 这个 DOM 节点，过 3s 左右之后才渲染出样式。这就证明了 CSS 是不会阻塞 DOM 的解析的，尽管 CSS 下载需要 3s，但这个过程中，浏览器不会傻等着 CSS 下载完，而是会解析 DOM 的。

浏览器是解析 DOM 生成 DOM Tree，结合 CSS 生成的 CSS Tree，最终组成 render tree，再渲染页面。

由此可见，在此过程中 CSS 完全无法影响 DOM Tree，因而无需阻塞 DOM 解析。

## CSS 阻塞页面渲染

如果 CSS 不会阻塞页面阻塞渲染，那么 CSS 文件下载之前，浏览器就会渲染出一个浅绿色的 div，之后再变成浅蓝色。（渲染是有成本的）。

>浏览器会尽量减少渲染的次数，CSS 顺理成章地阻塞页面渲染。


然而，事情总有奇怪的，请看这例子，HTML 头部结构如下：
``` html
<header>
 <link rel="stylesheet" href="*.css">
 <script src="*.js"></script>
</header>
```
* 答案是浏览器会转圈圈三秒，但此过程中不会打印任何东西

* 之后呈现出一个浅蓝色的 div

* 再打印出 null

其实这样做是有道理的，如果脚本的内容是获取元素的样式，宽高等 CSS 控制的属性，浏览器是需要计算的，也就是依赖于 CSS。浏览器也无法感知脚本内容到底是什么，为避免样式获取，因而只好等前面所有的样式下载完后，再执行 JS。因而造成了之前例子的情况。

所以，看官大人明白为何 script 与 link 同时在头部的话， script 在上可能会更好了么？之所以是可能，是因为如果 link 的内容下载更快的话，是没影响的，但反过来的话，JS 就要等待了，然而这些等待的时间是完全不必要的。

## JS 阻塞 DOM 解析

首先我们需要一个新的 JS 文件，插入header，内容如下：
``` javascript
// 执行数s的js
const div =document.querySelector('div');
console.log(div);
```

* 1.浏览器转圈圈一会，这过程中不会有任何东西出现。
* 2.之后打印出 null，
* 3.再出现一个浅绿色的 div。

> 现象就足以说明 JS 阻塞 DOM 解析了。

其实原因也很好理解，浏览器并不知道脚本的内容是什么，如果先行解析下面的 DOM，万一脚本内全删了后面的 DOM，浏览器就白干活了。更别谈丧心病狂的 document.write。浏览器无法预估里面的内容，那就干脆全部停住，等脚本执行完再干活就好了。

解决方案：
* 1.如果 JS 文件体积太大，同时你确定没必要阻塞 DOM 解析的话，不妨按需要加上 defer 或者 async 属性，此时脚本下载的过程中是不会阻塞 DOM 解析的。
* 2.如果是文件执行时间太长，不妨分拆一下代码，不用立即执行的代码，可以使用一下以前的黑科技：setTimeout()。当然，现代的浏览器很聪明，它会“偷看”之后的 DOM 内容，碰到如 link 、script 和 img 等标签时，它会帮助我们先行下载里面的资源，不会傻等到解析到那里时才下载。

> 浏览器遇到  script  标签时，会触发页面渲染

这个细节可能不少看官大人并不清楚，其实这才是解释上面为何 JS 执行会等待 CSS 下载的原因。先上例子,HTML 内 body 的结构如下：
``` html
<body>
 <div></div>
 <script src="/3s.js"></script>
 <style>
    div {
          background: lightgrey;
    }
 </style>
 <script src="/4s.js"></script>
 <link rel="stylesheet" href="3s.css">
</body>
```

这个例子也是很极端的例子，但不妨碍它透露给我们很多重要的信息。想象一下，页面会怎样呢？
答案是先浅绿色，再浅灰色，最后浅蓝色。

由此可见，每次碰到 script 标签时，浏览器都会渲染一次页面。这是基于同样的理由，浏览器不知道脚本的内容，因而碰到脚本时，只好先渲染页面，确保脚本能获取到最新的 DOM 元素信息，尽管脚本可能不需要这些信息。

## 小结
综上所述，我们得出这样的结论：
* CSS 不会阻塞 DOM 的解析，但会阻塞 DOM 渲染。
* JS 阻塞 DOM 解析，但浏览器会"偷看"DOM，预先下载相关资源。
* 浏览器遇到 script 且没有 defer 或 async 属性的 标签时，会触发页面渲染，因而如果前面 CSS 资源尚未加载完毕时，浏览器会等待它加载完毕在执行脚本。

所以，如果头部同时有 script 与 link 的情况下，最好将 script 放在 link 上面
