---
title: 浏览器的回流与重绘 (Reflow & Repaint)
tags: 'brower'
categories: 'brower'
top_img: '/img/brower.jpeg'
---

## 客户端渲染页面的过程</b>

* 1、处理 HTML 标记并构建 DOM 树。
* 2、处理 CSS 标记并构建 CSSOM 树。
* 3、将 DOM 与 CSSOM 合并成一个渲染树。
* 4、根据渲染树来布局，以计算每个节点的几何信息。
* 5、将各个节点绘制到屏幕上。

这五个步骤并不一定一次性顺序完成。如果 DOM 或 CSSOM 被修改，以上过程需要重复执行，这样才能计算出哪些像素需要在屏幕上进行重新渲染。实际页面中，CSS 与 JavaScript 往往会多次修改 DOM 和 CSSOM，下面就来看看它们的影响方式。
## 阻塞渲染：CSS 与 JavaScript

谈论资源的阻塞时，我们要清楚，现代浏览器总是并行加载资源。例如，当 HTML 解析器（HTML Parser）被脚本阻塞时，解析器虽然会停止构建 DOM，但仍会识别该脚本后面的资源，并进行预加载。
同时，由于下面两点：

* 1、默认情况下，CSS 被视为阻塞渲染的资源，这意味着浏览器将不会渲染任何已处理的内容，直至 CSSOM 构建完毕。
* 2、JavaScript 不仅可以读取和修改 DOM 属性，还可以读取和修改 CSSOM 属性。
* 存在阻塞的 CSS 资源时，浏览器会延迟 JavaScript 的执行和 DOM 构建。另外：


* 1、当浏览器遇到一个 script 标记时，DOM 构建将暂停，直至脚本完成执行。
* 2、JavaScript 可以查询和修改 DOM 与 CSSOM。
* 3、CSSOM 构建时，JavaScript 执行将暂停，直至 CSSOM 就绪。
* 所以，script 标签的位置很重要。实际使用时，可以遵循下面两个原则：

* 1、CSS 优先：引入顺序上，CSS 资源先于 JavaScript 资源。
* 2、JavaScript 应尽量少影响 DOM 的构建。

存在阻塞的 CSS 资源时，浏览器会延迟 JavaScript 的执行和 DOM 构建。另外：

所以，script 标签的位置很重要。实际使用时，可以遵循下面两个原则：

浏览器的发展日益加快，具体的渲染策略会不断进化，但了解这些原理后，就能想通它进化的逻辑。下面来看看 CSS 与 JavaScript 具体会怎样阻塞资源。

## defer

``` javascript
<script src="app1.js" defer></script>
<script src="app2.js" defer></script>
<script src="app3.js" defer></script>

```

defer 属性表示延迟执行引入的 JavaScript，即这段 JavaScript 加载时 HTML 并未停止解析，这两个过程是并行的。整个 document 解析完毕且 defer-script 也加载完成之后（这两件事情的顺序无关），会执行所有由 defer-script 加载的 JavaScript 代码，然后触发 DOMContentLoaded 事件。

defer 不会改变 script 中代码的执行顺序，示例代码会按照 1、2、3 的顺序执行。所以，defer 与相比普通 script，有两点区别：载入 JavaScript 文件时不阻塞 HTML 的解析，执行阶段被放到 HTML 标签解析完成之后。
## async

``` html
<script src="app.js" async></script><script src="ad.js" async></script><script src="statistics.js" async></script>

```

async 属性表示异步执行引入的 JavaScript，与 defer 的区别在于，如果已经加载好，就会开始执行——无论此刻是 HTML 解析阶段还是 DOMContentLoaded 触发之后。需要注意的是，这种方式加载的 JavaScript 依然会阻塞 load 事件。换句话说，async-script 可能在 DOMContentLoaded 触发之前或之后执行，但一定在 load 触发之前执行。
从上一段也能推出，多个 async-script 的执行顺序是不确定的。值得注意的是，向 document 动态添加 script 标签时，async 属性默认是 true

## 回流 (Reflow)
当Render Tree中部分或全部元素的尺寸、结构、或某些属性发生改变时，浏览器重新渲染部分或全部文档的过程称为回流。
会导致回流的操作
* 页面首次渲染
* 浏览器窗口大小发生改变
* 元素尺寸或位置发生改变
* 元素内容变化（文字数量或图片大小等等）
* 元素字体大小变化
* 添加或者删除可见的DOM元素
* 激活CSS伪类（例如：:hover）
* 查询某些属性或调用某些方法
一些常用且会导致回流的属性和方法
* clientWidth、clientHeight、clientTop、clientLeft
* offsetWidth、offsetHeight、offsetTop、offsetLeft
* scrollWidth、scrollHeight、scrollTop、scrollLeft
* scrollIntoView()、scrollIntoViewIfNeeded()
* getComputedStyle()
* getBoundingClientRect()
* scrollTo()
## 重绘 (Repaint)
当页面中元素样式的改变并不影响它在文档流中的位置时（例如：color、background-color、visibility等），浏览器会将新样式赋予给元素并重新绘制它，这个过程称为重绘。




## 性能影响
回流比重绘的代价要更高。
有时即使仅仅回流一个单一的元素，它的父元素以及任何跟随它的元素也会产生回流。
现代浏览器会对频繁的回流或重绘操作进行优化：
浏览器会维护一个队列，把所有引起回流和重绘的操作放入队列中，如果队列中的任务数量或者时间间隔达到一个阈值的，浏览器就会将队列清空，进行一次批处理，这样可以把多次回流和重绘变成一次。
当你访问以下属性或方法时，浏览器会立刻清空队列：
* clientWidth、clientHeight、clientTop、clientLeft
* offsetWidth、offsetHeight、offsetTop、offsetLeft
* scrollWidth、scrollHeight、scrollTop、scrollLeft
* width、height
* getComputedStyle()
* getBoundingClientRect()
因为队列中可能会有影响到这些属性或方法返回值的操作，即使你希望获取的信息与队列中操作引发的改变无关，浏览器也会强行清空队列，确保你拿到的值是最精确的。

## 如何避免CSS
* 避免使用table布局。
* 尽可能在DOM树的最末端改变class。
* 避免设置多层内联样式。
* 将动画效果应用到position属性为absolute或fixed的元素上。
* 避免使用CSS表达式（例如：calc()）。JavaScript
* 避免频繁操作样式，最好一次性重写style属性，或者将样式列表定义为class并一次性更改class属性。
* 避免频繁操作DOM，创建一个documentFragment，在它上面应用所有DOM操作，最后再把它添加到文档中。
* 也可以先为元素设置display: none，操作结束后再把它显示出来。因为在display属性为none的元素上进行的DOM操作不会引发回流和重绘。
* 避免频繁读取会引发回流/重绘的属性，如果确实需要多次使用，就用一个变量缓存起来。
* 对具有复杂动画的元素使用绝对定位，使它脱离文档流，否则会引起父元素及后续元素频繁回流。