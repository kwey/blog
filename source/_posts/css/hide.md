---
title: opacity、visibility、display
tags: 'css'
categories: 'web'
top_img: '/img/css.jpg'
---


<a href="http://www.w3school.com.cn/cssref/pr_opacity.asp" rel="nofollow noreferrer" target="_blank">opacity </a>用来设置透明度 

<a href="http://www.w3school.com.cn/css/pr_class_display.asp" rel="nofollow noreferrer" target="_blank">display</a> 定义建立布局时元素生成的显示框类型 

<a href="http://www.w3school.com.cn/cssref/pr_class_visibility.asp" rel="nofollow noreferrer" target="_blank">visibility</a> 用来设置元素是否可见。 

opacity、visibility、display 这三个属性分别取值 0、hidden、none 都能使元素在页面上看不见，但是他们在方方面面都还是有区别的。

## 注意：
>使用 opacity 和 display 属性时，父元素对子元素的影响很明显，子元素设置的 opacity 和 display 属性是不起作用的，显示的效果和父元素一样，而使用 visibility 属性时，子元素如果设置为 visibility:visible; 并没有受父元素的影响，可以继续显示出来。

> `回流`当页面中的一部分(或全部)因为元素的规模尺寸，布局，隐藏等改变而需要重新构建。这就称为回流(也有人会把回流叫做是重布局或者重排)。

每个页面至少需要一次回流，就是在页面第一次加载的时候。dispaly 属性会产生回流，而 opacity 和 visibility 属性不会产生回流。

>`重绘`当页面中的一些元素需要更新属性，而这些属性只是影响元素的外观，风格，而不会影响布局的时候，比如background-color。则称为重绘。

dispaly 和 visibility 属性会产生重绘，而 opacity 属性不一定会产生重绘。

<a href="https://segmentfault.com/q/1010000008983727" style="">元素提升为合成层后，transform 和 opacity 不会触发 repaint，如果不是合成层，则其依然会触发 repaint。</a> 

<a href="https://segmentfault.com/q/1010000008983727" target="_blank" style="">在 Blink 和 WebKit 内核的浏览器中，对于应用了 transition 或者 animation 的 opacity 元素，浏览器会将渲染层提升为合成层。

</a><a href="https://segmentfault.com/q/1010000008983727" style="">也可以使用 translateZ(0) 或者 translate3d(0,0,0) 来人为地强制性地创建一个合成层。</a>


<table border="0" width="100%" cellpadding="0" cellspacing="0"><tbody><tr><th> </th><th> opacity: 0</th><th>visibility: hidden </th><th>display: none </th></tr><tr><td> 是否占据页面空间</td><td><span style="color: rgb(249, 150, 59); font-size: large;">yes</td><td><span style="color: rgb(249, 150, 59); font-size: large;">yes</td><td>no </td></tr><tr><td> 对子元素是否有影响   </td><td><span style="color: rgb(249, 150, 59); font-size: large;">yes </td><td>no </td><td><span style="color: rgb(249, 150, 59); font-size: large;">yes </td></tr><tr><td> 自身绑定的事件是否继续触发</td><td><span style="color: rgb(249, 150, 59); font-size: large;">yes </td><td>no </td><td>no </td></tr><tr><td> 是否影响被遮挡的元素触发事件</td><td><span style="color: rgb(249, 150, 59); font-size: large;">yes </td><td>no </td><td>no </td></tr><tr><td> 属性值改变是否产生回流</td><td>no </td><td>no </td><td><span style="color: rgb(249, 150, 59); font-size: large;">yes </td></tr><tr><td> 属性值改变是否产生重绘</td><td><span style="color: rgb(194, 79, 74); font-size: large;">不一定 </td><td><span style="color: rgb(249, 150, 59); font-size: large;">yes </td><td><span style="color: rgb(249, 150, 59); font-size: large;">yes </td></tr><tr><td> 是否支持transition</td><td><span style="color: rgb(249, 150, 59); font-size: large;">yes </td><td><span style="color: rgb(249, 150, 59); font-size: large;">yes </td><td>no </td></tr></tbody></table>


