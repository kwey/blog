---
title: MessageChannel
tags: 'Javascript'
categories: 'web'
top_img: '/img/js.jpg'
---
> 在浏览器环境中，常见的 macro task 有 setTimeout、MessageChannel、postMessage、setImmediate。而常见的 micro task 有 MutationObsever 和 Promise.then。


## <a href="https://www.jianshu.com/p/4f07ef18b5d7" target="_blank">原文地址</a>

<a href="https://developer.mozilla.org/zh-CN/docs/Web/API/Worker/postMessage" target="_blank">Worker.postMessage()</a>

<a href="https://developer.mozilla.org/zh-CN/docs/Web/API/MessageChannel" target="_blank">MessageChannel</a>

## 使用：

var channel = new MessageChannel();

这样就创建了一个管道。
## 实例属性：

``` javascript
channel.port1
channel.port2
```


获取实例的两个端口，注意的是，两个端口都是只读的。

简单来说，MessageChannel创建了一个通信的管道，这个管道有两个端口，每个端口都可以通过postMessage发送数据，而一个端口只要绑定了onmessage回调方法，就可以接收从另一个端口传过来的数据。

一个简单的例子：

``` javascript
        var channel = new MessageChannel();
        var port1 = channel.port1;
        var port2 = channel.port2;
        port1.onmessage = function(event) {
                console.log("port1收到来自port2的数据：" + event.data);
            }
        port2.onmessage = function(event) {
                console.log("port2收到来自port1的数据：" + event.data);
            }

        port1.postMessage("发送给port2");
            port2.postMessage("发送给port1");
    
```

    
（1）深拷贝

MessageChannel还可以用于深拷贝，我们都知道深拷贝一般用JSON.parse(JSON.stringify(object))就可以解决了，



也知道这种方法的局限性：
<ul>
<li>会忽略 undefined
</li>
<li>不能序列化函数</li>
<li>不能解决循环引用的对象</li>
</ul>

undefined和函数会被忽略，而尝试拷贝循环引用的对象则会报错：
    
一般来说，这个方法都能解决大部分问题，而且性能也是最好的。

但是MessageChannel的postMessage传递的数据也是深拷贝的，这和web worker的postMessage一样。而且还可以拷贝undefined和循环引用的对象。

代码：

``` javascript
// 有undefined + 循环引用
    let obj = {
            a: 1,
            b: {
            c: 2,
            d: 3,
            },
        f: undefined
    }
    obj.c = obj.b;
    obj.e = obj.a
    obj.b.c = obj.c
    obj.b.d = obj.b
    obj.b.e = obj.b.c

    function deepCopy(obj) {
            return new Promise((resolve) => {
            const {port1, port2} = new MessageChannel();
        port2.onmessage = ev => resolve(ev.data);
        port1.postMessage(obj);
        });
    }

    deepCopy(obj).then((copy) => {           // 请记住`MessageChannel`是异步的这个前提！
            let copyObj = copy;
            console.log(copyObj, obj)
            console.log(copyObj == obj)
        });

```
但拷贝有函数的对象时，还是会报错：
    

    
这时候可能就要用到<a href="https://www.npmjs.com/package/lodash" target="_blank" rel="nofollow">lodash</a>这样的函数库了。


## （2）此特性在 Web Worker 中可以使用。

当我们使用多个web worker并想要在两个web worker之间实现通信的时候，MessageChannel也可以派上用场：



``` html
<script>
    var worker1 = new Worker("worker1.js");
    var worker2 = new Worker("worker2.js");
    var channel = new MessageChannel();
    worker1.postMessage({ port1: channel.port1 });
    worker2.postMessage({ port2: channel.port2 });
    worker2.onmessage = function(event) {
        console.log(event.data);
    }
</script>

```


``` javascript
self.onmessage = function(event) {
    const port1 = event.data.port1;
    setTimeout(function() {
        port1.postMessage("this is from worker2")
    }, 2000)
}

```


``` javascript
self.onmessage = function(event) {
    const port2 = event.ports;
    port2.onmessage = function(event) {
        self.postMessage(event.data);
    }
}

```
一开始我写出如上代码，结果报了这样的错误：

worker的数据传递是深复制的，这里报错说MessagePort不能复制。

于是我查了一下<a href="https://developer.mozilla.org/zh-CN/docs/Web/API/Worker/postMessage" target="_blank" rel="nofollow">Worker.postMessage()</a>。

发现这个方法有第二个可选的数组参数，可以将MessagePort传入，然后将控制权交给要发送到的worker。（这两句是我翻译的（如果还没有被大神改掉的话），翻译得不好别打我哈）

于是我把代码改为：

``` javascript
// index.html
<script>
    var w1 = new Worker("worker1.js");
    var w2 = new Worker("worker2.js");
    var ch = new MessageChannel();
    w1.postMessage("port1", [ch.port1]);
    w2.postMessage("port2", [ch.port2]);
    w2.onmessage = function(e) {
            console.log(e.data);
        }
</script>

```


``` javascript
// worker1.js
onmessage = function(e) {
    const  port = e.ports[0];
    port.postMessage("this is from worker1")        
}

```


``` javascript
// worker2.js
onmessage = function(e) {
    const port = e.ports[0];
    port.onmessage = function(e) {
        postMessage(e.data)
    }
}

```

    
由于在worker中无法使用console.log，因此我们通过给w2绑定onmessage回调函数来验证传递是否成功。最终我们可以看到控制台中输出

this is from worker1


传递的路径为：

w1=> ch1 => ch2 => w2
