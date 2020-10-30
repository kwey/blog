---
title: node ws模块
tags: 'ws'
categories: 'Node'
top_img: '/img/node.jpg'
---

<ul><li>认识 TCP
TCP 协议可以总结几个特点：<ul><li>有状态的长连接：客户端发起连接请求，服务端响应并建立连接，连接会一直保持直到一方主动断开。</li><li>主动性：建立起与客户端的连接后，服务端可主动向客户端发起调用。</li><li>信息安全性：同样可以使用 SSL 证书进行信息加密，访问时用 WSS 。</li><li>跨域：默认支持跨域。</li></ul>
</li><li><a href="https://github.com/michaelliao/learn-javascript/tree/master/samples/node/web/ws/ws-with-koa" target="_blank">编写聊天室源码ws-with-koa</a>
</li><li>服务端 API</li><li>安装第三方模块 ws：npm install ws</li><li>开启一个 WebSocket 的服务器，端口为 8080</li></ul>

``` javascript
var socketServer = require('ws').Server;
var wss = new socketServer({
    port: 8080
});

```
<ul><li>也可以利用 Express 来开启 WebSocket 的服务器</li></ul>

``` javascript
var app = require('express')();
var server = require('http').Server(app);

var socketServer = require('ws').Server;
var wss = new socketServer({server: server, port: 8080});
```

<ul><li>用 on 来进行事件监听</li><li>connection：连接监听，当客户端连接到服务端时触发该事件</li><li>close：连接断开监听，当客户端断开与服务器的连接时触发</li><li>message：消息接受监听，当客户端向服务端发送信息时触发该事件</li><li>send: 向客户端推送信息</li></ul>

``` javascript
wss.on('connection', function (client) {
    client.on('message', function (_message) {
        var _messageObj = JSON.parse(_message);
        //status = 1 表示正常聊天
        _messageObj.status = 1;
        this.message = _messageObj;
        //把客户端的消息广播给所有在线的用户
        wss.broadcast(_messageObj);
    });

    // 退出聊天  
    client.on('close', function() {  
        try{
            this.message = this.message || {};
            // status = 0 表示退出聊天
            this.message.status = 0;
            //把客户端的消息广播给所有在线的用户
            wss.broadcast(this.message);  
        }catch(e){  
                console.log('刷新页面了');  
            }  
    });  
});

//定义广播方法
wss.broadcast = function broadcast(_messageObj) {  
    wss.clients.forEach(function(client) { 
        client.send(JSON.stringify(_messageObj))
    });  
}; 
```


## 客户端 API

<ul><li>在支持 WebSocket 的浏览器下实例化 WebSocket ，参数为 WebSocket 服务器地址，建立与服务器的连接</li></ul>

``` javascript
if(!WebSocket){
    $('.connState').text("您的浏览器不支持WebSocket");
    return false;
} 
//连接 socket 服务器
var socket = new WebSocket('ws://localhost:8080');
```

<ul><li>onopen：当网络连接建立时触发该事件</li></ul>

``` javascript
//监听 socket 的连接
socket.onopen = function(){
    $('.connState').text("服务已连接 ws://localhost:8080");
}
```
<ul><li>onclose：当服务端关闭时触发该事件</li></ul>

``` javascript
//监听服务端断开
socket.onclose = function(){
    $('.connState').text("服务已断开");
    socket = null;
}
```

<ul><li>close: 在客户端断开与服务端的连接 socket.close();</li><li>onerror：当网络发生错误时触发该事件</li></ul>

``` javascript
//监听服务端异常
socket.onerror = function(){
    $('.connState').text("服务错误");
    socket = null;
}
```
<ul><li>onmessage：当接收到服务器发来的消息的时触发的事件，也是通信中最重要的一个监听事件</li></ul>

``` javascript
//监听服务端广播过来的消息
socket.onmessage = function(msg){
    var msgObj = JSON.parse(msg.data);
    if(msgObj.status == 0){
        $('<p>' 
        + msgObj.nickname 
        + '[' + msgObj.time 
        + ']退出聊天</p>').appendTo('.msgList');
    } else{
        $('<p>' 
        + msgObj.nickname 
        + '[' + msgObj.time 
        + ']：' + msgObj.message 
        + '</p>').appendTo('.msgList');
    }
}
```
<ul><li>send：向服务端推送消息</li></ul>

``` javascript
var sendMessage = function(_mess){
    if(socket){
        var myDate = new Date();
        var now = myDate.getMonth() 
        + '-' + myDate.getDate() 
        + ' ' + myDate.getHours() 
        + ':' + myDate.getMinutes() + ':' + myDate.getSeconds();                
        
        var mesObj = {
            nickname: $('#nickName').val(),
            message: _mess || $('#mesBox').val(),
            time: now
        }
        //向服务端发送消息
        socket.send(JSON.stringify(mesObj));
    }            
}
```