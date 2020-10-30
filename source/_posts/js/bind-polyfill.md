---
title: bind 的polyfill补充
tags: 'Javascript'
categories: 'web'
top_img: '/img/js.jpg'
---

<a href="https://github.com/mqyqingfeng/Blog/issues/12" target="_blank">原文地址</a>
        
bind 函数的三个特点：
<ol><li>1.返回一个函数</li><li>2.可以传入参数</li><li>3.一个绑定函数也能使用new操作符创建对象：这种行为就像把原函数当成构造器。提供的 this 值被忽略，同时调用时的参数被提供给模拟函数。</li></ol>

``` javascript
// Yes, it does work with `new funcA.bind(thisArg, args)`
if (!Function.prototype.bind) (function(){
        var ArrayPrototypeSlice = Array.prototype.slice;
        Function.prototype.bind = function(otherThis) {
        if (typeof this !== 'function') {
            // closest thing possible to the ECMAScript 5
            // internal IsCallable function
            throw new TypeError('Function.prototype.bind- what is trying to be bound is not callable');
        }

    var baseArgs= ArrayPrototypeSlice .call(arguments, 1),
        baseArgsLength = baseArgs.length,
        fToBind = this,
        fNOP    = function() {},
        fBound  = function() {
    
            // reset to default base arguments       baseArgs.length = baseArgsLength; 
            baseArgs.push.apply(baseArgs, arguments);
    
            // 当作为构造函数时，this 指向实例，此时结果为 true，
            将绑定函数的 this 指向该实例，可以让实例获得来自绑定函数的值       return fToBind.apply(
                fNOP.prototype.isPrototypeOf(this) ? this : otherThis, baseArgs
            );
        };
    
        if (this.prototype) {
            // Function.prototype doesn't have a prototype property
            fNOP.prototype = this.prototype; 
        }
        fBound.prototype = new fNOP();
    
        return fBound;
        };
    })();
    
```

运用一个空函数做中转，在直接修改 fBound.prototype 的时候，就不会修改绑定函数的 prototype

但是像fn.prototype.name.type = 8888

还是会修改绑定函数的prototype
但是原生的bind返回的函数是没有prototype属性的，
也就是无法访问到绑定函数的原型链


