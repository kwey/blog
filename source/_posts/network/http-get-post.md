---
title: HTTP 请求方法：GET 和 POST
tags: 'brower'
categories: 'brower'
top_img: '/img/brower.jpeg'
---

<ul><li>GET - 从指定的资源请求数据。</li><li>POST - 向指定的资源提交要被处理的数据。</li></ul><ul><li>

## GET 方法
请注意，查询字符串（名称/值对）是在 GET 请求的 URL 中发送的：

/test/demo_form.php?name1=value1&amp;name2=value2
有关 GET 请求的其他一些注释：</li><ul><li>GET 请求可被缓存</li><li>GET 请求保留在浏览器历史记录中</li><li>GET 请求可被收藏为书签</li><li>GET 请求不应在处理敏感数据时使用</li><li>GET 请求有长度限制</li><li>GET 请求只应当用于取回数据</li></ul></ul>
    
## POST 方法
    请注意，查询字符串（名称/值对）是在 POST 请求的 HTTP 消息主体中发送的：
    
    POST /test/demo_form.php HTTP/1.1
    Host: w3cschool.cn
    name1=value1&amp;name2=value2
    有关 POST 请求的其他一些注释：
    
<ul><li>    POST 请求不会被缓存</li><li>    POST 请求不会保留在浏览器历史记录中</li><li>    POST 不能被收藏为书签</li><li>    POST 请求对数据长度没有要求</li></ul>
        
## 比较 GET 与 POST

下面的表格比较了两种 HTTP 方法：GET 和 POST。<table><tbody><tr><th align="left" width="30%"> </th><th align="left" width="35%">GET</th><th align="left" width="35%">POST</th></tr><tr><td>后退按钮/刷新</td><td>无害</td><td>数据会被重新提交（浏览器应该告知用户数据会被重新提交）。
.</td></tr><tr><td>书签</td><td>可收藏为书签</td><td>不可收藏为书签
.</td></tr><tr><td>缓存</td><td>能被缓存</td><td>不能缓存
.</td></tr><tr><td>编码类型</td><td>application/x-www-form-urlencoded</td><td>application/x-www-form-urlencoded or multipart/form-data。为二进制数据使用多重编码。
.</td></tr><tr><td>历史</td><td>参数保留在浏览器历史中。</td><td>参数不会保存在浏览器历史中。
.</td></tr><tr><td>对数据长度的限制</td><td>是的。当发送数据时，GET 方法向 URL 添加数据；URL 的长度是受限制的（URL 的最大长度是 2048 个字符）。
.</td><td>无限制。
</td></tr><tr><td>对数据类型的限制</td><td>只允许 ASCII 字符。</td><td>没有限制。也允许二进制数据。
.</td></tr><tr><td>安全性</td><td>与 POST 相比，GET 的安全性较差，因为所发送的数据是 URL 的一部分。

在发送密码或其他敏感信息时绝不要使用 GET ！
.</td><td>POST 比 GET 更安全，因为参数不会被保存在浏览器历史或 web 服务器日志中。</td></tr><tr><td>可见性</td><td>数据在 URL 中对所有人都是可见的。</td><td>数据不会显示在 URL 中。</td></tr></tbody></table>
<hr>## 其他 HTTP 请求方法
下面的表格列出了其他一些 HTTP 请求方法：<table id="table1"><tbody><tr><th align="left" width="15%">方法</th><th align="left" width="85%">描述</th></tr><tr><td>HEAD</td><td>与 GET 相同，但只返回 HTTP 报头，不返回文档主体。
.</td></tr><tr><td>PUT</td><td>上传指定的 URI 表示。
.</td></tr><tr><td>DELETE</td><td>删除指定资源。
.</td></tr><tr><td>OPTIONS</td><td>返回服务器支持的 HTTP 方法。
.</td></tr><tr><td>CONNECT</td><td>把请求连接转换到透明的 TCP/IP 通道。</td></tr></tbody></table>