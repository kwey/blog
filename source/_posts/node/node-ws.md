---
title: 基于Node.js的WebSocket通信
tags: 'ws'
categories: 'Node'
top_img: '/img/node.jpg'
---

>  假如A,B,C,D用户均通过客户端连接到Websocket服务，其中每个人发的消息都需要将其通过Websocket转发给其他人，此场景类似于服务端将A的消息广播给组内其他用户。


## 服务端实现
首先来看服务端程序，具体的工作流程分以下几步：<li>创建一个WebSocketServer的服务，同时监听8080端口的连接请求。</li><li>每当有新的客户端连接该WebSocket成功时，便将该连接push到连接池的数组中。</li><li>监听message事件，当该事件发生时，遍历连接池，以连接为单位将该消息转发到对应的客户端</li><li>监听close事件，当该事件发生时，将该连接移出连接池</li>
``` javascript
# 服务端代码
var WebSocketServer = require('ws').Server,
wss = new WebSocketServer({port: 8080});

// 连接池
var clients = [];

wss.on('connection', function(ws) {
        // 将该连接加入连接池
        clients.push(ws);
        ws.on('message', function(message) {
            // 广播消息
            clients.forEach(function(ws1){
                if(ws1 !== ws) {
                    ws1.send(message);
                }
        })
    });

    ws.on('close', function(message) {
            // 连接关闭时，将其移出连接池
            clients = clients.filter(function(ws1){
                return ws1 !== ws
            })
    });
});

```


## 客户端实现
```html
<html>
<input type="text" id="text">
<input type="button" onclick="sendMessage()" value="online">
<script>
    var ws = new WebSocket("ws://localhost:8080");

    ws.onopen = function (e) {
            console.log('Connection to server opened');
        }
    ws.onmessage = function(event) { 
            console.log('Client received a message', event); 
        }; 
    ws.onclose = function (e) {
            console.log('connection closed.');
        }
    function sendMessage() {
            ws.send(document.getElementById('text').value);
        }
</script>
</html>
```

# 如何发现用户？
通过上述的demo可以看到，WebSocket都是基于连接的，也就是说我们知道data是从那个connection发过来，但并不知道使用客户端的是A或者B,如果A只想给B发消息，此时我们就需要在Server端能够标识用户身份和连接的对应关系。
于是，需要在客户端连接到WebSocket之后，紧接着再发一次请求，告诉Server我的user_id是多少，Server将此user_id与connection之间的关系存储在hashmap中，至此就建立了user_id与connection的对应关系。当需要发送消息给对应的客户端，从此hashmap中取出对应用户的connection信息，调用其send方法发出消息即可。
依赖包
npm install hashmap</blockquote>

## 服务端实现
```javascript
var WebSocketServer = require('ws').Server, webSocketServer = new WebSocketServer({port: 8080});
var HashMap = require('hashmap');

// record the client
var userConnectionMap = new HashMap();
var connectNum = 0;

// connection
webSocketServer.on('connection', function(ws) {
    ++connectNum;
    console.log('A client has connected. current connect num is : ' + connectNum);
    ws.on('message', function(message) {
        var objMessage = JSON.parse(message);
        var strType  = objMessage['type'];

        switch(strType) {
            case 'online' : 
                userConnectionMap.set(objMessage['from'], ws);
                break;
            default:
                var targetConnection = userConnectionMap.get(objMessage['to']);
                if (targetConnection) {
                    targetConnection.send(message);
                }
    }
});

ws.on('close', function(message) {
    var objMessage = JSON.parse(message);
    userConnectionMap.remove(objMessage['from']);
});
});
```