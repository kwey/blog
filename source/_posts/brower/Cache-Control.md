---
title: Cache-Control
tags: 'brower'
categories: 'brower'
top_img: '/img/brower.jpeg'
---

> 浏览器缓存里, Cache-Control是金字塔顶尖的规则, 它藐视一切其他设置, 只要其他设置与其抵触, 一律覆盖之.

语法为: “Cache-Control : cache-directive”.
Cache-directive共有如下12种(其中请求中指令7种, 响应中指令 9 种):
<table border="0" width="100%" cellpadding="0" cellspacing="0"><tbody><tr><th> cache-directive</th><th>描述 </th><th>存储策略 </th><th>过期策略 </th><th>请求 </th><th>响应 </th></tr><tr><td> Public</td><td>  资源将被客户端和代理服务器缓存</td><td>yes </td><td> </td><td> </td><td>yes </td></tr><tr><td> Private</td><td> 资源将被客户端缓存、代理服务器不缓存</td><td>yes </td><td> </td><td> </td><td> yes</td></tr><tr><td> no-sore</td><td> 请求和相应都不缓存</td><td> yes</td><td> </td><td> yes</td><td> yes</td></tr><tr><td> no-cache</td><td> 客户端缓存内容，但是是否使用缓存则需要经过协商缓存来验证决定</td><td> yes</td><td> yes</td><td> yes</td><td> yes</td></tr><tr><td> max-age</td><td> 客户端可以接收生存期不大于指定时间（以秒为单位）的响应
</td><td> yes</td><td> yes</td><td> yes</td><td> yes</td></tr><tr><td> s-maxage</td><td> 覆盖max-age 或者 Expires 头，但是仅适用于共享缓存(比如各个代理)，并且私有缓存中它被忽略。</td><td> yes</td><td> yes</td><td></td><td> yes</td></tr><tr><td> max-stale</td><td> 指示客户端可以接收超出超时期间的响应消息</td><td> </td><td> yes</td><td> yes</td><td> yes</td></tr><tr><td> min-fresh</td><td> 指示客户端可以接收响应时间小于当前时间加上指定时间的响应。</td><td> </td><td> yes</td><td> yes</td><td> </td></tr><tr><td> must-revalidate</td><td> 缓存必须在使用之前验证旧资源的状态，并且不可使用过期资源。</td><td> </td><td> yes</td><td></td><td> yes</td></tr><tr><td> proxy-revalidate</td><td> 与must-revalidate作用相同，但它仅适用于共享缓存（例如代理），并被私有缓存忽略。</td><td> </td><td> yes</td><td> </td><td> yes</td></tr><tr><td> only-if-cached</td><td> 表明客户端只接受已缓存的响应，并且不要向原始服务器检查是否有更新的拷贝</td><td> </td><td> </td><td> yes</td><td> </td></tr><tr><td> no-transform</td><td> 不得对资源进行转换或转变</td><td> </td><td> </td><td> yes</td><td> yes</td></tr></tbody></table>

假设所请求资源于4月5日缓存, 且在4月12日过期.

当max-age 与 max-stale 和 min-fresh 同时使用时, 它们的设置相互之间独立生效, 最为保守的缓存策略总是有效. 这意味着, 如果max-age=10 days, max-stale=2 days, min-fresh=3 days, 那么:

根据max-age的设置, 覆盖原缓存周期, 缓存资源将在4月15日失效(5+10=15);

根据max-stale的设置, 缓存过期后两天依然有效, 此时响应将返回110(Response is stale)状态码, 缓存资源将在4月14日失效(12+2=14);

根据min-fresh的设置, 至少要留有3天的新鲜期, 缓存资源将在4月9日失效(12-3=9);

由于客户端总是采用最保守的缓存策略, 因此, 4月9日后, 对于该资源的请求将重新向服务器发起验证.
注： 单一计算不会叠加

## 用户行为对缓存的影响

<table border="0" width="100%" cellpadding="0" cellspacing="0"><tbody><tr><th>操作 </th><th>Expires/ Cache-Control</th><th>Etag / If-None-Match </th></tr><tr><td> 地址栏回车</td><td> yes</td><td> yes</td></tr><tr><td> 页面链接跳转</td><td> yes</td><td> yes</td></tr><tr><td> 新打开窗口</td><td> yes</td><td> yes</td></tr><tr><td> 前进后退</td><td> yes</td><td> yes</td></tr><tr><td> F5</td><td> 无效</td><td> yes</td></tr><tr><td> Ctrl+F5</td><td> 无效</td><td> 无效</td></tr></tbody></table>