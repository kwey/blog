---
title: fetch方法
tags: 'Javascript'
categories: 'web'
top_img: '/img/js.jpg'
---
> fetch方法返回一个Promise对象, 根据 Promise Api 的特性, fetch可以方便地使用then方法将各个处理逻辑串起来, 使用 Promise.resolve() 或 Promise.reject() 方法将分别返会肯定结果的Promise或否定结果的Promise, 从而调用下一个then 或者 catch. 一但then中的语句出现错误, 也将跳到catch中.

fetch() 必须接受一个参数——资源的路径。无论请求成功与否，它都返回一个 Promise 对象，resolve 对应请求的 <a href="https://developer.mozilla.org/zh-CN/docs/Web/API/Response" title="Fetch API 的Response接口呈现了对一次请求的响应数据">Response</a>。你也可以传一个可选的第二个参数init（参见 <a href="https://developer.mozilla.org/zh-CN/docs/Web/API/Request" title="你可以使用  Request.Request() ?构造函数创建一个Request 对象，但是你可能会遇到一个 Request 对象作为其它 API 的操作被返回，比如一个 service worker的FetchEvent.request。">Request</a>）。
### mode

fetch可以设置不同的模式使得请求有效. 模式可在fetch方法的第二个参数对象中定义.
``` javascript
fetch(url, {mode: 'cors'});
```

可定义的模式如下:<ul><li>same-origin: 表示同域下可请求成功; 反之, 浏览器将拒绝发送本次fetch, 同时抛出错误 “TypeError: Failed to fetch(…)”.</li><li>cors: 表示同域和带有CORS响应头的跨域下可请求成功. 其他请求将被拒绝.</li><li>cors-with-forced-preflight: 表示在发出请求前, 将执行preflight检查.</li><li>no-cors: 常用于跨域请求不带CORS响应头场景, 此时响应类型为 “opaque”.</li></ul>
除此之外, 还有两种不太常用的mode类型, 分别是 navigate , websocket , 它们是 <a href="https://html.spec.whatwg.org/multipage/" target="_blank" rel="external">HTML标准</a> 中特殊的值, 这里不做详细介绍.
## <a href="http://louiszhai.github.io/2016/11/02/fetch/#header" title="header"></a>header

fetch获取http响应头非常easy. 如下:
``` javascript
fetch(url).then(function(response) { 
    console.log(response.headers.get('Content-Type'));
});
```

设置http请求头也一样简单.
``` javascript
var headers = new Headers();
headers.append("Content-Type", "text/html");
fetch(url,{
    headers: headers
});
```

header的内容也是可以被检索的.
``` javascript
var header = new Headers({
    "Content-Type": "text/plain"
});
console.log(header.has("Content-Type")); //true
console.log(header.has("Content-Length")); //false
```
### <a href="http://louiszhai.github.io/2016/11/02/fetch/#post" title="post">post</a>

在fetch中发送post请求, 同样可以在fetch方法的第二个参数对象中设置.
``` javascript
var headers = new Headers();
headers.append("Content-Type", "application/json;charset=UTF-8");
fetch(url, {
        method: 'post',
        headers: headers,
        body: JSON.stringify({
        date: '2016-10-08',
        time: '15:16:00'
        })
});
```
### <a href="http://louiszhai.github.io/2016/11/02/fetch/#credentials" title="credentials">credentials</a>

跨域请求中需要带有cookie时, 可在fetch方法的第二个参数对象中添加credentials属性, 并将值设置为”include”.
``` javascript
fetch(url,{
    credentials: 'include'
});
```

除此之外, credentials 还可以取以下值:<ul><li>omit: 缺省值, 默认为该值.</li><li>same-origin: 同源, 表示同域请求才发送cookie.</li></ul><h4 id="catch"><a href="http://louiszhai.github.io/2016/11/02/fetch/#catch" title="catch"></a>catch

同 XMLHttpRequest 一样, 无论服务器返回什么样的状态码(chrome中除407之外的其他状态码), 它们都不会进入到错误捕获里. 也就是说, 此时, XMLHttpRequest 实例不会触发 onerror 事件回调, fetch 不会触发 reject. 通常只在网络出现问题时或者ERR_CONNECTION_RESET时, 它们才会进入到相应的错误捕获里. (其中, 请求返回状态码为407时, chrome浏览器会触发onerror或者reject掉fetch.)<h4 id="cache"><a href="http://louiszhai.github.io/2016/11/02/fetch/#cache" title="cache"></a>cache

cache表示如何处理缓存, 遵守http规范, 拥有如下几种值:<ul><li>default: 表示fetch请求之前将检查下http的缓存.</li><li>no-store: 表示fetch请求将完全忽略http缓存的存在. 这意味着请求之前将不再检查下http的缓存, 拿到响应后, 它也不会更新http缓存.</li><li>no-cache: 如果存在缓存, 那么fetch将发送一个条件查询request和一个正常的request, 拿到响应后, 它会更新http缓存.</li><li>reload: 表示fetch请求之前将忽略http缓存的存在, 但是请求拿到响应后, 它将主动更新http缓存.</li><li>force-cache: 表示fetch请求不顾一切的依赖缓存, 即使缓存过期了, 它依然从缓存中读取. 除非没有任何缓存, 那么它将发送一个正常的request.</li><li>only-if-cached: 表示fetch请求不顾一切的依赖缓存, 即使缓存过期了, 它依然从缓存中读取. 如果没有缓存, 它将抛出网络错误(该设置只在mode为”same-origin”时有效).</li></ul>
如果fetch请求的header里包含 If-Modified-Since, If-None-Match, If-Unmodified-Since, If-Match, 或者 If-Range 之一, 且cache的值为 default , 那么fetch将自动把 cache的值设置为 "no-store" .

## async/await语法
async 用于声明一个异步函数, 该函数需返回一个 Promise 对象. 而 await 通常后接一个 Promise对象, 需等待该 Promise 对象的 resolve() 方法执行并且返回值后才能继续执行. (如果await后接的是其他对象(非promise), 便会立即执行)

await 返回promise里面 resolve的值
var word = '123',
    url = '...'; 
(async ()=>{ 
    try { 
            let res = await fetch(url, {mode: 'no-cors'});//等待fetch被resolve()后才能继续执行

                        console.log(res);
    } catch(e) {
            
        console.log(e);
        }
})();

使用await后, 可以直接得到返回值, 不必写 .then(callback) , 也不必写 .catch(error) 了, 更可以使用 try catch 标准语法捕获错误.
由于await采用的是同步的写法, 看起来它就和alert函数一样, 可以自动阻塞上下文. 因此它可以重复执行多次, 就像上述代码②一样.
可以看到, await/async 同步阻塞式的写法解决了完全使用 Promise 的一大痛点——不同Promise之间共享数据问题. Promise 需要设置上层变量从而实现数据共享, 而 await/async 就不存在这样的问题, 只需要像写alert一样书写就可以了.
值得注意的是, await 只能用于 async 声明的函数上下文中. 如下 forEach 中, 是不能直接使用await的.
``` javascript
let array = [0,1,2,3,4,5];
(async ()=>{
    array.forEach(function(item){
    await wait(1000);//这是错误的写法, 因await不在async声明的函数上下文中
    console.log(item);
    });
})();
```

如果是试图将async声明的函数作为回调传给forEach，该回调将同时触发多次，回调内部await依然有效，只是多次的await随着回调一起同步执行了，这便不符合我们阻塞循环的初衷。如下：
``` javascript
const fn = async (item)=>{
        await wait(1000); // 循环中的多个await同时执行，因此等待1s后将同时输出数组各个元素
        console.log(item);
    };
array.forEach(fn);
```

正确的写法如下：
``` javascript
(async ()=>{
        for(let i=0,len=array.length;i<len;i++){
        await wait(1000);
        console.log(array[i]);
        }
})();
```


<h3 id="如何弥补Fetch的不足">如何弥补Fetch的不足</h3>

fetch基于Promise, Promise受限, fetch也难幸免. ES6的Promise基于 <a href="https://promisesaplus.com/" target="_blank" rel="external">Promises/A+</a> 规范 (对规范感兴趣的同学可选读 <a href="http://www.cnblogs.com/fsjohnhuang/p/4135149.html" target="_blank" rel="external">剖析源码理解Promises/A规范</a> ), 它只提供极简的api, 没有 timeout 机制, 没有 progress 提示, 没有 deferred 处理 (这个可以被async/await替代).<h4 id="fetch-jsonp"><a href="http://louiszhai.github.io/2016/11/02/fetch/#fetch-jsonp" title="fetch-jsonp"></a>fetch-jsonp

除此之外, fetch还不支持jsonp请求. 不过办法总比问题多, 万能的开源作者提供了 <a href="https://github.com/camsong/fetch-jsonp" target="_blank" rel="external">fetch-jsonp</a> 库, 解决了这个问题.
fetch-jsonp 使用起来非常简单. 如下是安装:
``` javascript
npm install fetch-jsonp --save-dev
```

如下是使用:
``` javascript
fetchJsonp(url, {
    timeout: 3000,
    jsonpCallback: 'callback'
}).then(function(response) {
    console.log(response.json());
}).catch(function(e) {
    console.log(e)
});
```
## <a href="http://louiszhai.github.io/2016/11/02/fetch/#abort" title="abort">abort</a>
    
由于Promise的限制, fetch 并不支持原生的abort机制, 但这并不妨碍我们使用 Promise.race() 实现一个.

> Promise.race(iterable) 方法返回一个Promise对象, 只要 iterable 中任意一个Promise 被 resolve 或者 reject 后, 外部的Promise 就会以相同的值被 resolve 或者 reject.

支持性: 从 chrome33, Firefox29, Safari7.1, Opera20, EdgeHTML12(并非Edge版本) 起, Promise就被完整的支持. Promise.race()也随之可用. 下面我们来看下实现.
``` javascript
var _fetch = (function(fetch){
        return function(url,options){
        var abort = null;
        var abort_promise = new Promise((resolve, reject)=>{
            abort = () => {
            reject('abort.');
            console.info('abort done.');
            };
    });
    var promise = Promise.race([
            fetch(url,options),
            abort_promise
        ]);
    promise.abort = abort;
    return promise;
    };
})(fetch);
```

然后, 使用如下方法测试新的fetch.
``` javascript
var p = _fetch('https://www.baidu.com',{mode:'no-cors'});
p.then(function(res) {
    console.log('response:', res);
}, function(e) {
    console.log('error:', e);
});
p.abort();
//"abort done."
//"error: abort."
```

以上, fetch请求后, 立即调用abort方法, 该promise被拒绝, 符合预期. 细心的同学可能已经注意到了, “p.abort();” 该语句我是单独写一行的, 没有链式写在then方法之后. 为什么这么干呢? 这是因为then方法调用后, 返回的是新的promise对象. 该对象不具有abort方法, 因此使用时要注意绕开这个坑.<h4 id="timeout"><a href="http://louiszhai.github.io/2016/11/02/fetch/#timeout" title="timeout"></a>timeout

同上, 由于Promise的限制, fetch 并不支持原生的timeout机制, 但这并不妨碍我们使用 Promise.race() 实现一个.
下面是一个简易的版本.
``` javascript
function timer(t){
    return new Promise(resolve=>setTimeout(resolve, t))
    .then(function(res) {
    console.log('timeout');
    });
}
var p = fetch('https://www.baidu.com',{mode:'no-cors'});
Promise.race([p, timer(1000)]);
//"timeout"
```

实际上, 无论超时时间设置为多长, 控制台都将输出log “timeout”. 这是因为, 即使fetch执行成功, 外部的promise执行完毕, 此时 setTimeout 所在的那个promise也不会reject.
下面我们来看一个类似xhr版本的timeout.
``` javascript
var _fetch = (function(fetch){
    return function(url,options){
            var abort = null,
                timeout = 0;
            var abort_promise = new Promise((resolve, reject)=>{
                abort = () => {
                reject('timeout.');
                console.info('abort done.');
                };
        });
        var promise = Promise.race([
            fetch(url,options),
            abort_promise
        ]);
        promise.abort = abort;
        Object.defineProperty(promise, 'timeout',{
            set: function(ts){
                if((ts=+ts)){
                    timeout = ts;
                    setTimeout(abort,ts);
                }
            },
            get: function(){
                    return timeout;
            }
        });
        return promise;
    };
})(fetch);
```

然后, 使用如下方法测试新的fetch.
``` javascript
var p = _fetch('https://www.baidu.com',{mode:'no-cors'});
p.then(function(res) {
    console.log('response:', res);
}, function(e) {
    console.log('error:', e);
});
p.timeout = 1;
//"abort done."
//"error: timeout."
```
<h4 id="progress"><a href="http://louiszhai.github.io/2016/11/02/fetch/#progress" title="progress"></a>progress

xhr的 onprogress 让我们可以掌控下载进度, fetch显然没有提供原生api 做类似的事情. 不过 Fetch中的Response.body 中实现了getReader()方法用于读取原始字节流, 该字节流可以循环读取, 直到body下载完成. 因此我们完全可以模拟fetch的progress.

以下是 stackoverflow 上的一段代码, 用于模拟fetch的progress事件. 为了方便测试, 请求url已改为本地服务.(<a href="https://stackoverflow.com/questions/35711724/upload-progress-indicators-for-fetch" target="_blank">原文请戳 javascript - Progress indicators for fetch? - Stack Overflow</a>)


``` javascript
function consume(reader) {
    var total = 0
    return new Promise((resolve, reject) => {
    function pump() {
        reader.read().then(({done, value}) => {
        if (done) {
            resolve();
            return;
        }
    total += value.byteLength;
    console.log(`received
        ${value.byteLength} bytes
        (${total} bytes in total)`
    );
    pump();
    }).catch(reject)
    }
    pump();
    });
}
fetch('http://localhost:10101/notification/',{mode:'no-cors'})
    .then(res => consume(res.body.getReader()))
    .then(() => console.log(
"consumed the entire body 
without keeping the whole thing in memory!"
    ))
        .catch(e => console.log("something went wrong: " + e));
```

以下是日志
```
received 32768 bytes (32768 bytes in total)
received 32768 bytes (32768*2 bytes in total)
received 32768 bytes (32768*3 bytes in total)
received 32768 bytes (32768*4 bytes in total)
received 32768 bytes (32768*5 bytes in total)
received 32768 bytes (32768*6 bytes in total)
received 32768 bytes (32768*7 bytes in total)
received 32768 bytes (32768*8 bytes in total)
received 32768 bytes (32768*9 bytes in total)
...
received 919 bytes (532480 bytes in total)


consumed the entire body without keeping the whole thing in memory!
```
我们不妨来对比下, 使用xhr的onprogress事件回调, 输出如下:

received 32768 bytes (32768 bytes in total)

received 499712 bytes (532480 bytes in total)

适当增加响应body的size, 发现xhr的onprogress事件回调依然只执行两次. 通过多次测试发现其执行频率比较低, 远不及fetch progress.
