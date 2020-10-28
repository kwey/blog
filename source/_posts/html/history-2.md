---
title: 使用pushState()改变url而不刷新
tags: 'html'
categories: 'web'
top_img: '../../img/html.jpg'
---
> HTML5提供history接口，把URL以state的形式添加或者替换到浏览器中，其实现函数正是 pushState 和 replaceState。


## pushState 例子

pushState() 的基本参数是：
``` javascript
    window.history.pushState(state, title, url);
```
其中state和title都可以为空，但是推荐不为空，应当创建state来配合popstate监听。
例如，我们通过pushState现改变URL而不刷新页面。
``` javascript
//code from http://caibaojian.com/book/
var state = ({
    
url: ~href, 
title: ~title,
 ~additionalKEY: ~additionalVALUE
});

window.history.pushState(state, ~title, ~href);
```
其中带有“~”符号的是自定义内容。就可以把这个~href（URL）推送到浏览器的历史里。
如果想要改变网页的标题，应该：

``` javascript
    document.title= ~newTitle;
```
注意只是pushState是不能改变网页标题的哦。

## Demo 演示

<button onclick="history.pushState( null, null, '/book/');">点我试试</button> 

（实现函数onclick = history.pushState( null, null, '/book/'); ）。
实际上这个博客在文章之间也部署了这个技术。

## replaceState 同理
``` javascript
window.history.replaceState( state, ~title, ~href);
```

## pushState、replaceState 的区别

pushState()可以创建历史，可以配合popstate事件，而replaceState()则是替换掉当前的URL，不会产生历史。
限制因素
只能用同域的URL替换，例如你不能用http://baidu.com去替换http://google.com。
而且state对象不存储不可序列化的对象如DOM。

## Ajax 配合 pushState 例子
现在用Ajax + pushState来提供全新的<a href="http://caibaojian.com/t/ajax" title="View all posts in ajax" target="_blank">ajax</a>调用风格。以<a href="http://caibaojian.com/jquery/" title="jQuery手册">jQuery</a>为例，为了<a href="http://caibaojian.com/t/seo" title="seo">seo</a>需要，
应该为a标签的onclick添加方法。<a href="http://caibaojian.com/book/">·</a>
``` javascript
$("~target a").click(function(evt){
    evt.preventDefault(); // 阻止默认的跳转操作

    var uri=$(this).attr('href');
    var newTitle=ajax_Load(uri);// 你自定义的Ajax加载函数，例如它会返回newTitle
    document.title=newTitle; // 分配新的页面标题

    if (history.pushState) {
        var state=({
            url: uri, title: newTitle
        });

    window.history.pushState(state, newTitle, uri);

    } else { 
        window.location.href="#!"+~fakeURI; 
    } // 如果不支持，使用旧的解决方案

return false;

});

function ajax_Load(uri){ ... return newTitle; } 
// 你自定义的ajax函数，例如它会返回newTitle
```
即可完成pushState。至于新标题newTitle的获取就是另外的问题了，例如你可以为a标签分配&gt;·
另外如果需要对新加载的页面的连接同样使用这个ajax，则需要对新内容的a标签重新部署，例如
``` javascript
$("~newContentTarget a").click(function(evt){
    // ... 
});
```
## pushState 配合 popstate 监听
> 注意：
调用history.pushState()或者history.replaceState()不会触发popstate事件. popstate事件只会在浏览器某些行为下触发, 比如点击后退、前进按钮(或者在JavaScript中调用history.back()、history.forward()、history.go()方法).
``` javascript
//绑定事件处理函数.
window.onpopstate = function(event) {  
    console.log("location: " + document.location + ", state: " + JSON.stringify(event.state));
};

history.pushState({page: 1}, "title 1", "?page=1");
//添加并激活一个历史记录条目 http://example.com/example.html?page=1,条目索引为1

history.pushState({page: 2}, "title 2", "?page=2");
//添加并激活一个历史记录条目 http://example.com/example.html?page=2,条目索引为2

history.replaceState({page: 3}, "title 3", "?page=3"); 
//修改当前激活的历史记录条目 http://ex..?page=2 变为 http://ex..?page=3,条目索引为3

history.back(); 
// 弹出 "location: http://example.com/example.html?page=1, state: {"page":1}"

history.back(); 
// 弹出 "location: http://example.com/example.html, state: null

history.go(2);  
// 弹出 "location: http://example.com/example.html?page=3, state: {"page":3}

```