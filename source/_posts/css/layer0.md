---
title: css图层
tags: 'css'
categories: 'web'
top_img: '../../img/css.jpg'
---

[浏览器层合成与页面渲染优化](https://juejin.im/post/6844903959425974280#heading-6)

## 渲染对象（RenderObject）
一个 DOM 节点对应了一个渲染对象，渲染对象依然维持着 DOM 树的树形结构。一个渲染对象知道如何绘制一个 DOM 节点的内容，它通过向一个绘图上下文（GraphicsContext）发出必要的绘制调用来绘制 DOM 节点。

## 渲染层（RenderLayer）

这是浏览器渲染期间构建的第一个层模型，处于相同坐标空间（z轴空间）的渲染对象，都将归并到同一个渲染层中，因此根据层叠上下文，不同坐标空间的的渲染对象将形成多个渲染层，以体现它们的层叠关系。所以，对于满足形成层叠上下文条件的渲染对象，浏览器会自动为其创建新的渲染层。能够导致浏览器为其创建新的渲染层的，包括以下几类常见的情况：

* 根元素 document
* 有明确的定位属性（relative、fixed、sticky、absolute）
* opacity < 1
* 有 CSS fliter 属性
* 有 CSS mask 属性
* 有 CSS mix-blend-mode 属性且值不为 normal
* 有 CSS transform 属性且值不为 none
* backface-visibility 属性为 hidden
* 有 CSS reflection 属性
* 有 CSS column-count 属性且值不为 auto或者有 CSS column-width 属性且值不为 auto
* 当前有对于 opacity、transform、fliter、backdrop-filter 应用动画
* overflow 不为 visible


DOM 节点和渲染对象是一一对应的，满足以上条件的渲染对象就能拥有独立的渲染层。当然这里的独立是不完全准确的，并不代表着它们完全独享了渲染层，由于不满足上述条件的渲染对象将会与其第一个拥有渲染层的父元素共用同一个渲染层，因此实际上，这些渲染对象会与它的部分子元素共用这个渲染层。

#  图形层（GraphicsLayer）

GraphicsLayer 其实是一个负责生成最终准备呈现的内容图形的层模型，它拥有一个图形上下文（GraphicsContext），GraphicsContext 会负责输出该层的位图。存储在共享内存中的位图将作为纹理上传到 GPU，最后由 GPU 将多个位图进行合成，然后绘制到屏幕上，此时，我们的页面也就展现到了屏幕上。
所以 GraphicsLayer 是一个重要的渲染载体和工具，但它并不直接处理渲染层，而是处理合成层。

## 合成层（CompositingLayer）
满足某些特殊条件的渲染层，会被浏览器自动提升为合成层。合成层拥有单独的 GraphicsLayer，而其他不是合成层的渲染层，则和其第一个拥有 GraphicsLayer 的父层共用一个。
那么一个渲染层满足哪些特殊条件时，才能被提升为合成层呢？这里列举了一些常见的情况：

* 3D transforms：translate3d、translateZ 等
* video、canvas、iframe 等元素
* 通过 Element.animate() 实现的 opacity 动画转换
* 通过 СSS 动画实现的 opacity 动画转换
* position: fixed
* 具有 will-change 属性
* 对 opacity、transform、fliter、backdropfilter 应用了 animation 或者 transition

利用 will-change 属性，将 CPU 消耗高的渲染元素提升为一个新的合成层，才能开启 GPU 加速的，因此你也可以使用 transform: translateZ(0) 来解决这个问题。

> 这里值得注意的是，不少人会将这些合成层的条件和渲染层产生的条件混淆，这两种条件发生在两个不同的层处理环节，是完全不一样的。


#  隐式合成

上边提到，满足某些显性的特殊条件时，渲染层会被浏览器提升为合成层。除此之外，在浏览器的 Composite 阶段，还存在一种隐式合成，部分渲染层在一些特定场景下，会被默认提升为合成层。
对于隐式合成，CSS GPU Animation 中是这么描述的：

>This is called implicit compositing: One or more non-composited elements that should appear above a composited one in the stacking order are promoted to composite layers.

> (一个或多个非合成元素应出现在堆叠顺序上的合成元素之上，被提升到合成层。)

这句话可能不好理解，它其实是在描述一个交叠问题（overlap）。举个例子说明一下：

两个 absolute 定位的 div 在屏幕上交叠了，根据 z-index 的关系，其中一个 div 就会”盖在“了另外一个上边。

这个时候，如果处于下方的 div 被加上了 CSS 属性：transform: translateZ(0)，就会被浏览器提升为合成层。提升后的合成层位于 Document 上方，假如没有隐式合成，原本应该处于上方的 div 就依然还是跟 Document 共用一个 GraphicsLayer，层级反而降了，就出现了元素交叠关系错乱的问题。

所以为了纠正错误的交叠顺序，浏览器必须让原本应该”盖在“它上边的渲染层也同时提升为合成层。


