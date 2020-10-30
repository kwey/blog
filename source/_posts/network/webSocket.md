---
title: WebSocket
tags: 'brower'
categories: 'brower'
top_img: '/img/brower.jpeg'
---

> WebSocket是HTML5新增的协议，它的目的是在浏览器和服务器之间建立一个不受限的双向通信的通道，比如说，服务器可以在任意时刻发送消息给浏览器。

## WebSocket协议
WebSocket并不是全新的协议，而是利用了HTTP协议来建立连接。我们来看看WebSocket连接是如何创建的。

首先，WebSocket连接必须由浏览器发起，因为请求协议是一个标准的HTTP请求，格式如下：
```javascript 
GET ws://localhost:3000/ws/chat HTTP/1.1
Host: localhost
Upgrade: websocket
Connection: Upgrade
Origin: http://localhost:3000
Sec-WebSocket-Key: client-random-string
Sec-WebSocket-Version: 13

```

该请求和普通的HTTP请求有几点不同：<li>GET请求的地址不是类似/path/，而是以ws://开头的地址；</li><li>请求头Upgrade: websocket和Connection: Upgrade表示这个连接将要被转换为WebSocket连接；</li><li>Sec-WebSocket-Key是用于标识这个连接，并非用于加密数据；</li><li>Sec-WebSocket-Version指定了WebSocket的协议版本。</li>
随后，服务器如果接受该请求，就会返回如下响应：
```javascript
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: server-random-string

```

该响应代码101表示本次连接的HTTP协议即将被更改，更改后的协议就是Upgrade: websocket指定的WebSocket协议。

版本号和子协议规定了双方能理解的数据格式，以及是否支持压缩等等。如果仅使用WebSocket的API，就不需要关心这些。

现在，一个WebSocket连接就建立成功，浏览器和服务器就可以随时主动发送消息给对方。消息有两种，一种是文本，一种是二进制数据。通常，我们可以发送JSON格式的文本，这样，在浏览器处理起来就十分容易。

为什么WebSocket连接可以实现全双工通信而HTTP连接不行呢？实际上HTTP协议是建立在TCP协议之上的，TCP协议本身就实现了全双工通信，但是HTTP协议的请求－应答机制限制了全双工通信。WebSocket连接建立以后，其实只是简单规定了一下：接下来，咱们通信就不使用HTTP协议了，直接互相发数据吧。

安全的WebSocket连接机制和HTTPS类似。首先，浏览器用wss://xxx创建WebSocket连接时，会先通过HTTPS创建安全的连接，然后，该HTTPS连接升级为WebSocket连接，底层通信走的仍然是安全的SSL/TLS协议。

## 浏览器
很显然，要支持WebSocket通信，浏览器得支持这个协议，这样才能发出ws://xxx的请求。目前，支持WebSocket的主流浏览器如下：<ul><li>Chrome</li><li>Firefox</li><li>IE >= 10</li><li>Sarafi >= 6</li><li>Android >= 4.4</li><li>iOS >= 8</li></ul>
# 客户端的简单示例

## <a name="#-E6-9C-8D-E5-8A-A1-E5-99-A8"></a>
WebSocket 的用法相当简单。
下面是一个网页脚本的例子（点击<a href="http://jsbin.com/muqamiqimu/edit?js,console" target="_blank">这里</a>看运行结果），基本上一眼就能明白。
```javascript
var ws = new WebSocket("<a href="wss://echo.websocket.org/">wss://echo.websocket.org</a>");
ws.onopen = function(evt) { 
    console.log("Connection open ..."); 
    ws.send("Hello WebSockets!");
};

ws.onmessage = function(evt) {
    console.log( "Received Message: " + evt.data);
    ws.close();
};

ws.onclose = function(evt) {
    console.log("Connection closed.");
};      

```
# 客户端的 API

WebSocket 客户端的 API 如下。

## 1、 WebSocket 构造函数

WebSocket 对象作为一个构造函数，用于新建 WebSocket 实例。
```javascript

var ws = new WebSocket('ws://localhost:8080');

```
执行上面语句之后，客户端就会与服务器进行连接。
实例对象的所有属性和方法清单，参见<a href="https://developer.mozilla.org/en-US/docs/Web/API/WebSocket" target="_blank">这里</a>。

## 2、 webSocket.readyState

readyState属性返回实例对象的当前状态，共有四种。<ul><li>CONNECTING：值为0，表示正在连接。</li><li>OPEN：值为1，表示连接成功，可以通信了。</li><li>CLOSING：值为2，表示连接正在关闭。</li><li>CLOSED：值为3，表示连接已经关闭，或者打开连接失败。</li></ul></blockquote>
下面是一个示例。

```javascript
switch (ws.readyState) {
        case WebSocket.CONNECTING:
        // do something
        break;
        case WebSocket.OPEN:
        // do something
        break;
        case WebSocket.CLOSING:
        // do something
        break;
        case WebSocket.CLOSED:
        // do something
        break;
        default:
        // this never happens
        break;
    }

```

## 3、webSocket.onopen

实例对象的onopen属性，用于指定连接成功后的回调函数。
```javascript

ws.onopen = function () {
    ws.send('Hello Server!');
}
```
如果要指定多个回调函数，可以使用addEventListener方法。
```

ws.addEventListener('open', function (event) {
    ws.send('Hello Server!');
});

```

## 4、 webSocket.onclose

实例对象的onclose属性，用于指定连接关闭后的回调函数。
```javascript
ws.onclose = function(event) {
    var code = event.code;
    var reason = event.reason;
    var wasClean = event.wasClean;
    // handle close event
};

ws.addEventListener("close", function(event) {
    var code = event.code;
    var reason = event.reason;
    var wasClean = event.wasClean;
    // handle close event
});

```
## 5、 webSocket.onmessage
实例对象的onmessage属性，用于指定收到服务器数据后的回调函数。
```javascript
ws.onmessage = function(event) {
    var data = event.data;
    // 处理数据
};

ws.addEventListener("message", function(event) {
    var data = event.data;
    // 处理数据
});

```
注意，服务器数据可能是文本，也可能是二进制数据（blob对象或Arraybuffer对象）。
```javascript

ws.onmessage = function(event){
    if(typeof event.data === String) {
    console.log("Received data string");
    }

if(event.data instanceof ArrayBuffer){
    var buffer = event.data;
    console.log("Received arraybuffer");
    }
}

```
除了动态判断收到的数据类型，也可以使用binaryType属性，显式指定收到的二进制数据类型。
```javascript

// 收到的是 blob 数据
ws.binaryType = "blob";
ws.onmessage = function(e) {
    console.log(e.data.size);
};

// 收到的是 ArrayBuffer 数据
ws.binaryType = "arraybuffer";
ws.onmessage = function(e) {
    console.log(e.data.byteLength);
};

```

## 6、 webSocket.send()
实例对象的send()方法用于向服务器发送数据。
发送文本的例子。
```javascript

ws.send('your message');

```
发送 Blob 对象的例子。
```javascript

var file = document
    .querySelector('input[type="file"]')
    .files[0];
ws.send(file);

```
发送 ArrayBuffer 对象的例子。
```javascript

// Sending canvas ImageData as ArrayBuffer
var img = canvas_context.getImageData(0, 0, 400, 320);
var binary = new Uint8Array(img.data.length);
for (var i = 0; i &lt; img.data.length; i++) {
    binary[i] = img.data[i];
}
ws.send(binary.buffer);

```
</blockquote>

## 7、 webSocket.bufferedAmount

实例对象的bufferedAmount属性，表示还有多少字节的二进制数据没有发送出去。它可以用来判断发送是否结束。
```javascript
var data = new ArrayBuffer(10000000);
socket.send(data);

if (socket.bufferedAmount === 0) {
    // 发送完毕
} else {
    // 发送还没结束
}
```

## 8、 webSocket.onerror

实例对象的onerror属性，用于指定报错时的回调函数。
```javascript
socket.onerror = function(event) {
    // handle error event
};

socket.addEventListener("error", function(event) {
    // handle error event
});

```
# 服务端的实现

## 
WebSocket 服务器的实现，可以查看维基百科的<a href="https://en.wikipedia.org/wiki/Comparison_of_WebSocket_implementations" target="_blank">列表</a>。
常用的 Node 实现。<ul><li><a href="https://github.com/uNetworking/uWebSockets" target="_blank">µWebSockets</a>
</li><li><a href="https://socket.io/" target="_blank">Socket.IO</a>
</li><li><a href="https://github.com/theturtle32/WebSocket-Node" target="_blank">WebSocket-Node</a>
</li><li><a href="https://github.com/websockets/ws" target="_blank">WS</a>
</li></ul>