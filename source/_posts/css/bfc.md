---
title: CSS中的BFC
tags: 'css'
categories: 'web'
top_img: '../../img/css.jpg'
---

> BFC（Block Formatting Context）直译为“块级格式化范围 

文档流其实分为定位流、浮动流和普通流三种。而`普通流其实就是指BFC中的FC`。
`FC``是页面中的一块渲染区域`，有一套渲染规则，决定了其`子元素如何布局，以及和其他元素之间的关系和作用。`常见的FC有BFC、IFC（行级格式化上下文），还有GFC（网格布局格式化上下文）和FFC（自适应格式化上下文

## 触发BFC
满足下列条件之一就可触发BFC

* 根元素，即HTML元素
* float的值不为none
* overflow的值不为visible
* display的值为inline-block、table-cell、table-caption
* position的值为absolute或fixed 　

## BFC布局规则：
1.内部的Box会在垂直方向，一个接一个地放置。
2.Box垂直方向的距离由margin决定。属于同一个BFC的两个相邻Box的margin会发生重叠
3.每个元素的margin box的左边， 与包含块border box的左边相接触(对于从左往右的格式化，否则相反)。即使存在浮动也是如此。
4.BFC的区域不会与float box重叠。
5.BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。反之也如此。
6.计算BFC的高度时，浮动元素也参与计算

## BFC有哪些作用：

* 自适应两栏布局
* 可以阻止元素被浮动元素覆盖
* 可以包含浮动元素——清除内部浮动
* 分属于不同的BFC时可以阻止margin重叠