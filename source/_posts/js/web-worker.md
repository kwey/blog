---
title: Web Worker
tags: 'javascript'
categories: 'Javascript'
top_img: '../../img/js.jpg'
---
> 为 JavaScript 创造多线程环境，允许主线程创建 Worker 线程，将一些任务分配给Worker 线程

Web Worker 有以下几个使用注意点。
* 同源限制

    分配给 Worker 线程运行的脚本文件，必须与主线程的脚本文件同源。
* DOM 限制

    Worker 线程所在的全局对象，与主线程不一样，无法读取主线程所在网页的 DOM 对象，也无法使用document、window、parent这些对象。但是，Worker 线程可以navigator对象和location对象。
* 通信联系

    Worker 线程和主线程不在同一个上下文环境，它们不能直接通信，必须通过消息完成。
* 脚本限制

    Worker 线程不能执行alert()方法和confirm()方法，但可以使用 XMLHttpRequest 对象发出 AJAX 请求。
* 文件限制

    Worker 线程无法读取本地文件，即不能打开本机的文件系统（file://），它所加载的脚本，必须来自网络。


## 主线程
主线程采用new命令，调用Worker()构造函数，新建一个 Worker 线程。

``` javascript
var worker = new Worker('work.js');

```

Worker()构造函数的参数是一个脚本文件，（之后会介绍行内的写法以及基于webpack的配置）该文件就是 Worker 线程所要执行的任务。由于 Worker 不能读取本地文件，所以这个脚本必须来自网络。如果下载没有成功（比如404错误），Worker 就会默默地失败。

然后，主线程调用worker.postMessage()方法，向 Worker 发消息。

``` javascript
worker.postMessage('Hello World');
worker.postMessage({method: 'echo', args: ['Work']});
```

worker.postMessage()方法的参数，就是主线程传给 Worker 的数据。它可以是各种数据类型，包括二进制数据。
接着，主线程通过worker.onmessage指定监听函数，接收子线程发回来的消息。

``` javascript
worker.onmessage = function (event) {
    console.log('Received message ' + event.data);
    doSomething();
}

```
    
上面代码中，事件对象的data属性可以获取 Worker 发来的数据。
Worker 完成任务以后，主线程就可以把它关掉。

``` javascript
worker.terminate();
```


## Worker 线程
Worker 线程内部需要有一个监听函数，监听message事件。

``` javascript
self.addEventListener('message', function (e) {
    self.postMessage('You said: ' + e.data);
}, false);

```
    
上面代码中，self代表子线程自身，即子线程的全局对象。因此，等同于下面两种写法。

``` javascript
// 写法一
this.addEventListener('message', function (e) {
    this.postMessage('You said: ' + e.data);
}, false);

// 写法二
addEventListener('message', function (e) {
    postMessage('You said: ' + e.data);
}, false);

```
    
除了使用self.addEventListener()指定监听函数，也可以使用self.onmessage指定。监听函数的参数是一个事件对象，它的data属性包含主线程发来的数据。self.postMessage()方法用来向主线程发送消息。

根据主线程发来的数据，Worker 线程可以调用不同的方法，下面是一个例子。

``` javascript
self.addEventListener('message', function (e) {
    var data = e.data;
    switch (data.cmd) {
        case 'start':
            self.postMessage('WORKER STARTED: ' + data.msg);
            break;
        case 'stop':
            self.postMessage('WORKER STOPPED: ' + data.msg);
            self.close(); // Terminates the worker.
            break;
        default:
            self.postMessage('Unknown command: ' + data.msg);
    };
}, false);

```

上面代码中，self.close()用于在 Worker 内部关闭自身。

## Worker 加载脚本
Worker 内部如果要加载其他脚本，有一个专门的方法importScripts()。

``` javascript
importScripts('script1.js');

```

该方法可以同时加载多个脚本。

``` javascript
importScripts('script1.js', 'script2.js');

```

## 错误处理
主线程可以监听 Worker 是否发生错误。如果发生错误，Worker 会触发主线程的error事件。

``` javascript
worker.onerror(function (event) {
    console.log([
    'ERROR: Line ', e.lineno, ' in ', e.filename, ': ', e.message
    ].join(''));
});

// 或者
worker.addEventListener('error', function (event) {
        // ...
});

```
    
Worker 内部也可以监听error事件。
    
    
## 数据通信
主线程与 Worker 之间的通信内容，可以是文本，也可以是对象。需要注意的是，这种通信是拷贝关系，即是传值而不是传址，Worker 对通信内容的修改，不会影响到主线程。事实上，浏览器内部的运行机制是，先将通信内容串行化，然后把串行化后的字符串发给 Worker，后者再将它还原。

主线程与 Worker 之间也可以交换二进制数据，比如 File、Blob、ArrayBuffer 等类型，也可以在线程之间发送。
``` javascript
// 主线程
var uInt8Array = new Uint8Array(new ArrayBuffer(10));
for (var i = 0; i < uInt8Array.length; ++i) {
    uInt8Array[i] = i * 2; // [0, 2, 4, 6, 8,...]
}
worker.postMessage(uInt8Array);

// Worker 线程
self.onmessage = function (e) {
    var uInt8Array = e.data;
    postMessage('Inside worker.js: uInt8Array.toString() = ' + uInt8Array.toString());
    postMessage('Inside worker.js: uInt8Array.byteLength = ' + uInt8Array.byteLength);
};
```

拷贝方式发送二进制数据，会造成性能问题。比如，主线程向 Worker 发送一个 500MB 文件，默认情况下浏览器会生成一个原文件的拷贝。为了解决这个问题，JavaScript 允许主线程把二进制数据直接转移给子线程，但是一旦转移，主线程就无法再使用这些二进制数据了，这是为了防止出现多个线程同时修改数据的麻烦局面。这种转移数据的方法，叫做<a href="http://www.w3.org/html/wg/drafts/html/master/infrastructure.html#transferable-objects" target="_blank">Transferable Objects</a>。这使得主线程可以快速把数据交给 Worker，对于影像处理、声音处理、3D 运算等就非常方便了，不会产生性能负担。

如果要直接转移数据的控制权，就要使用下面的写法。

``` javascript
// Transferable Objects 格式
worker.postMessage(arrayBuffer, [arrayBuffer]);

// 例子
var ab = new ArrayBuffer(1);
worker.postMessage(ab, [ab]);
```
    
    
    
## Web Worker行内写法
``` javascript
function createWorker(f) {
    var blob = new Blob(['(' + f.toString() +')()']);
    var url = window.URL.createObjectURL(blob);
    var worker = new Worker(url);
    return worker;
}

var pollingWorker = createWorker(function (e) {
    var cache;

    function compare(new, old) { ... };

    setInterval(function () {
        fetch('/my-api-endpoint').then(function (res) {
            var data = res.json();
    
            if (!compare(data, cache)) {
            cache = data;
            self.postMessage(data);
            }
    })
    }, 1000)
});

pollingWorker.onmessage = function () {
        // render data
}

pollingWorker.postMessage('init');
```


## 基于webpack的worker-loader
``` bash
npm i -D worker-loader
```

``` javascript
// App.js：
    import Worker from './worker.js';
    const worker = new Worker();

    worker.postMessage({ a: 1 });
    worker.onmessage = function (event) {};
    worker.addEventListener("message", function (event) {});
```
// worker.js
``` javascript
self.addEventListener('message', function (e) {
    self.postMessage('You said: ' + e.data);
}, false);
```


// webpack.config.js
``` javascript
{
    module: {
    rules: [
        {
            test: /\\.worker\\.js$/,
            use: { 
                loader: 'worker-loader' 
                options: {
                    name: string,//默认‘[hash].worker.js 输出js文件名
                    inline: boolean, // 默认false，是否将 worker 内联为一个 BLOB
                    fallback: boolean, // 默认false，是否需要支持非 worker 环境的回退
                    publicPath: string, // 默认值：null,重写 worker 脚本的下载路径
                }
            }
        }]
    }
}
    
```
    
## 集成 TypeScript 

## <a aria-label="集成 TypeScript" href="https://webpack.docschina.org/loaders/worker-loader/#%E9%9B%86%E6%88%90-typescript"></a>


集成 TypeScript，在导出 worker 时，需要声明一个自定义模块

``` javascript
// typings/custom.d.ts
declare module "worker-loader!*" {
    class WebpackWorker extends Worker {
        constructor();
    }

    export default WebpackWorker;
}

```

``` javascript
// Worker.ts
const ctx: Worker = self as any;

// 发送数据到父线程
ctx.postMessage({ foo: "foo" });

// 响应父线程的消息
ctx.addEventListener("message", (event) => console.log(event));
// App.ts
import Worker from "worker-loader!./Worker";

const worker = new Worker();

worker.postMessage({ a: 1 });
worker.onmessage = (event) => {};

worker.addEventListener("message", (event) => {});
```
## <a href="https://webpack.docschina.org/loaders/worker-loader/" target="_blank">Webpack文档地址</a>
这么用： 
``` javascript
import Workers from 'worker-loader?inline=true&fallback=false!./danmaku-worker.rev';
```