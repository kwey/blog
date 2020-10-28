---
title: rel=nofollow属性
tags: 'html'
categories: 'web'
top_img: '../../img/js.jpg'
---

> nofollow是HTML元标签(meta)的content属性和链接标签(a)的rel属性的一个值，告诉机器(爬虫)无需追踪目标页，为了对抗blogspam(博客垃圾留言信息)，Google推荐使用nofollow，告诉搜索引擎爬虫无需抓取目标页，同时告诉搜索引擎无需将的当前页的Pagerank传递到目标页。但是如果你是通过sitemap直接提交该页面，爬虫还是会爬取，这里的nofollow只是当前页对目标页的一种态度，并不代表其他页对目标页的态度。

## nofollow的使用
nofollow有
两种用法：
* 用于meta元标签：`<meta name="robots" content="nofollow" />`，告诉爬虫该页面上所有链接都无需追踪。
* 用于a标签：`<a href="login.aspx" rel="nofollow">登录</a>`,告诉爬虫该页面无需追踪。
### nofollow的作用

nofollow主要有三个作用：
* 防止不可信的内容，最常见的是博客上的垃圾留言与评论中为了获取外链的垃圾链接，为了防止页面指向一些拉圾页面和站点。
* 付费链接：为了防止付费链接影响Google的搜索结果排名，Google建议使用nofollow属性。
* 引导爬虫抓取有效的页面：避免爬虫抓取一些无意义的页面，影响爬虫抓取的效率。


### PR修剪(Pagerank Sculpting)

nofollow的滥用，一些SEO为了做到搜索引擎的最大优化，通过nofollow来控制PR的流动，可以很好的优化一些特定页面。当然这种优化比较适合一些已经积淀了相当数量PR的老站点。为了防止PR修剪和nofollow的滥用，Google已经减弱了nofollow的作用，以前的nofollow不仅仅不会造成PR流动，同时不会造成PR损失，现在的nofollow规定虽然也不会造成PR流向目标页，但是原本流向的目标页的将会损失掉。比方当前页PR为1，而且页面上有10个链接，其中一个是nofollow的链接，根据先前的nofollow的规定，每个非nofollow链接指向的目标页将获得1/9的PR，含nofollow的链接不能获得PR，而根据现在Google对nofollow的新规定，非nofollow链接指向的目标页只能获得1/10，nofollow链接同样不能获得PR，也就是损失了1/10的PR。

## SEO建议      

nofollow在Google的作用已经很弱，所以SEO要控制站点的PR的流动，避免链接指向垃圾页面，只能靠人工审核的方法。

## SEO基础更多阅读

<a target="_blank" href="http://www.cnblogs.com/shuchao/archive/2009/09/17/url-normalization.html">URL规范化(URL normalization)</a>

<a target="_blank" href="http://www.cnblogs.com/shuchao/archive/2009/09/14/keyword-stuffing.html">关键词堆砌(Keyword Stuffing)</a>

<a target="_blank" href="http://www.cnblogs.com/shuchao/archive/2009/09/13/cloaking.html">隐藏页(Cloaking)、伪装、障眼法</a>

<a target="_blank" href="http://www.cnblogs.com/shuchao/archive/2009/09/12/Doorway-Page.html">桥页(Bridge Page)，门页(Doorway Page)</a>

<a target="_blank" href="http://www.cnblogs.com/shuchao/archive/2009/09/10/link-rot.html">链接无效(link rot)、链接出错</a>
