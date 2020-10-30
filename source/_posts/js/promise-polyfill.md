---
title: Promise-Polyfill源码解析
tags: 'Javascript'
categories: 'web'
top_img: '/img/js.jpg'
---
<a href="https://github.com/taylorhakes/promise-polyfill" target="_blank">https://github.com/taylorhakes/promise-polyfill</a>
        
        
        
我们平时都是以new Promise(params)的形式使用Promise的，说明Promise是一个构造函数，那我们就从构造函数为入口来分析Promise-polyfill源码。如下：

``` javascript
/**
* @constructor
* @param {Function} fn
*/
function Promise(fn) {
    if (!(this instanceof Promise))
    throw new TypeError('Promises must be constructed via new');
    if (typeof fn !== 'function') throw new TypeError('not a function');
    /** @type {!number} */
this._state = 0;
/** @type {!boolean} */
this._handled = false;
/** @type {Promise|undefined} */
this._value = undefined;
/** @type {!Array&lt;!Function>} */
this._deferreds = [];

doResolve(fn, this);
}

```

第一个if语句说明Promise必须以构造函数形式被调用，第二个if语句则说明Promise的唯一参数fn必须是函数类型。接下来是四个对象属性的定义，我们逐一来看：

``` javascript
 /** @type {!number} */
this._state = 0;
复制代码
```

_state属性定义了Promise的状态，我们都知道Promise有pending、fulfilled、rejected三种状态，在源码里，三种状态分别对应_state值为0、1、2。此外，源码中还有_state值为3，第四种内部状态，这个我们后面遇到再讲。

``` javascript
 /** @type {!boolean} */
    this._handled = false;

```

_handled属性的类型为Boolean，初始值为false，其代表Promise是否被处理。

``` javascript
 /** @type {Promise|undefined} */
    this._value = undefined;

```

_value属性的类型为Promise或undefined，初始值为undefined，其代表。

``` javascript
 /** @type {!Array&lt;!Function>} */
    this._deferreds = [];

```

_deferreds属性的类型为Array，初始值为空数组，其作用我们后面遇到再讲，现在只要注意其数组中存放的值为Function。
最后是一个函数调用：

``` javascript
 doResolve(fn, this);

```

将Promise的参数fn与代表当前对象的this作为参数，调用了deResolve函数。至此，我们可以发现整个构造函数只是在做一些必要的检查和属性定义，并没有做什么处理，那关键点应该就在最后的函数调用。我们来看看deResolve函数都做了些什么：

``` javascript
 function doResolve(fn, self) {
        var done = false;
        try {
        fn(
            function(value) {
            if (done) return;
            done = true;
            resolve(self, value);
            },
        function(reason) {
            if (done) return;
            done = true;
            reject(self, reason);
            }
    );
    } catch (ex) {
        if (done) return;
        done = true;
        reject(self, ex);
        }
}

```

总体上看，首先定义了一个变量done，初始值为false，接下来是一个try..catch语句，我们先来分析try部分:

``` javascript
 try {
        fn(
            function(value) {
            if (done) return;
            done = true;
            resolve(self, value);
            },
        function(reason) {
            if (done) return;
            done = true;
            reject(self, reason);
            }
    );
} 

```

上面讲过fn就是构造函数的参数，也就是我们new Promise时传入的回调函数:

``` javascript
new Promise(function(resolve, reject) {
    // do something
});

```

我们用resolve, reject替换fn中的两个参数，结果变成:

``` javascript
fn(resolve, reject);

```

也就是说try部分总共就做了一件事，就是讲我们传入的回调函数执行了，并传入了两个回调函数作为参数。这里特别注意一点，到目前为止，并没有涉及到异步之类的，所以我们可以知道Promise构造函数内的代码是同步执行的！
那么传入的两个回调函数是什么时候被执行的呢？其实就是在我们调用resolve(value)或reject(reason)的时候：

``` javascript
new Promise(function(resolve, reject) {
    // do something
    // resolve(value);
    reject(reason);
});

```

我们再看两个回调函数的内部逻辑，两者唯一的差别就是最后调用的函数不同，我们先看相同的部分:

``` javascript
 if (done) return;
    done = true;

```

done变量为true则直接退出函数，否则将done置为true，再执行下面代码。所以我们知道，done变量的作用就是为了防止resolve()和reject()被同时调用。因为Promise标准规定了，其状态只能从pending->fulfilled或pending->rejected。
再看不同部分:

``` javascript
 resolve(self, value);

```

``` javascript
 reject(self, reason);

```

这两个函数的参数是当前对象和我们传入的值，也就是我们所说的完成的值和拒绝的原因，由此我们可以预测，调用这两个函数会将Promsie的状态变为fulfilled或rejected。
最后看catch部分:

``` javascript
catch (ex) {
    if (done) return;
    done = true;
    reject(self, ex);
}

```

其逻辑完全与try部分的第二个回调函数一样，其实就是说，调用Promsie构造函数如果抛出异常，则Promise就会变为rejected状态。
接下来分析resolve与reject函数：

``` javascript
function resolve(self, newValue) {
    try {
        if (newValue === self)
            throw new TypeError('A promise cannot be resolved with itself.');
        if (
            newValue &&
            (typeof newValue === 'object' || typeof newValue === 'function')
        ) {
            var then = newValue.then;
            if (newValue instanceof Promise) {
            self._state = 3;
            self._value = newValue;
            finale(self);
            return;
            } else if (typeof then === 'function') {
            doResolve(bind(then, newValue), self);
            return;
            }
        }
        self._state = 1;
        self._value = newValue;
        finale(self);
    } catch (e) {
        reject(self, e);
    }
}

```

整个代码被try...catch包裹，先看只有一行代码的catch部分:

``` javascript
 reject(self, e);

```

整个resolve函数抛出异常，都会调用reject函数，所以我们也明白了，resolve后的状态不一定就是fulfilled，也可能是rejected，但reject后的状态一定是rejected。
再看try部分，我们先跳过前面二个条件判断，直接看最后的部分：

``` javascript
 self._state = 1;
    self._value = newValue;
    finale(self);

```

_state属性赋值为1，前面讲过，1代表状态为fulfilled。_value保存了完成的值，最后将当前对象作为参数调用了finale函数。finale主要为then方法做准备的，与Promise构造函数关系不大，我们讲then方法时再分析。
然后是第一个条件检测：

``` javascript
  if (newValue === self)
        throw new TypeError('A promise cannot be resolved with itself.');

```

newValue是我们传入的完成的值，self是当前的Promise对象，也就是说，完成的值不能是当前对象本身。就是下面这种情况:

``` javascript
const promise = new Promise(function (resolve, reject) {
        setTimeout(function () {
        resolve(promise);
        }, 0);
});

```

用异步的原因是保证resolve(promise)时，promise已经被赋值。
第二个条件主要是处理特殊类型的完成值：

``` javascript
if ( newValue && (typeof newValue === 'object' || typeof newValue === 'function') ) {
        var then = newValue.then;
        //...
} 

```

如果newValue是对象或函数类型，就将其then属性保存在then变量中。

往下讲之前，我们需要知道一个概念：thenable类型，拥有then方法的对象或函数。这个定义其实是借鉴了鸭子类型：如果它看起来像一只鸭子，并且叫起来相一致鸭子，那么它一定是一只鸭子。为什么要提这个呢？因为我们需要判断一个值是否是纯粹的Promise对象，具体由来就不讲了，推荐大家去看《你不知道的JavaScript 中卷》。

知道thenable类型，我们就清楚下面的代码是做什么的了：

``` javascript
if (newValue instanceof Promise) {
    self._state = 3;
    self._value = newValue;
    finale(self);
    return;
}

```

为什么这个判断要先判断？因为Promise也有then方法，所以要先判断值是不是纯粹的Promise。以_state=3标记。再判断是否是thenable类型：

``` javascript
 else if (typeof then === 'function') {
        doResolve(bind(then, newValue), self);
        return;
}

```

其中bind函数为Function.prototype.bind的polyfill：

``` javascript
function bind(fn, thisArg) {
        return function() {
        fn.apply(thisArg, arguments);
        };
}

```

即将以newValue为this的函数和当前对象作为参数再次调用doResolve函数，这么做的原因，是如果Promise的完成的值是Promise或thenable类型，那么最终状态取决于Promise或thenable的状态。



