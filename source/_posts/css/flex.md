---
title: Flex 布局教程
tags: 'css'
categories: 'web'
top_img: '/img/css.jpg'
---

## 6个属性设置在容器上:

* flex-direction: row （默认 row |row-reverse |column |column-reverse;
* flex-wrap nowrap （默认 nowrap | wrap | wrap-reverse; 
* flex-flow: `前两个只和，默认row nowrap`` <flex-direction> || <flex-wrap>`
* justify-content flex-start （默认 flex-start | flex-end |center\nspace-between | space-around; 
* align-items stretch （默认 flex-start |flex-end |center\nbaseline | stretch; 
* align-content stretch （默认 flex-start |flex-end |center\nspace-between |space-around |stretch; 

## 6个属性设置在项目上。

* order 属性定义项目的排列顺序。数值越小，排列越靠前，默认为0。
* flex-grow: `<number>`/* default 0 */ 
* flex-shrink: `<number>`/* default 1 */ 
* flex-base: `<length>` | auto; /* default auto */ 
* flex: `[<'flex-grow'><'flex-shrink'>?||<'flex-basis'>] `
性有两个快捷值： auto  ( 1 1 auto ) 和 none ( 0 0 auto )。
* align-self: flex-to | flex-start | flex-end |center | baseline | stretch; 


<a href="http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html?utm_source=tuicool" target="_blank">图文教程请点击这</a>

