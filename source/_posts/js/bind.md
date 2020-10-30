---
title: Function.prototype.bind()
tags: 'Javascript'
categories: 'web'
top_img: '/img/js.jpg'
---

> bind()方法创建一个新的函数，在调用时设置this关键字为提供的值。并在调用新函数时，将给定参数列表作为原函数的参数序列的前若干项。


<a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/bind" target="_blank">原文地址</a>
        
## 语法：
``` javascript
function.bind(thisArg[, arg1[, arg2[, ...]]])
```
### thisArg：
调用绑定函数时作为this参数传递给目标函数的值。 如果使用<a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/new" title="new 运算符创建一个用户定义的对象类型的实例或具有构造函数的内置对象的实例。">new</a>运算符构造绑定函数，则忽略该值。当使用bind在setTimeout中创建一个函数（作为回调提供）时，作为thisArg传递的任何原始值都将转换为object。如果bind函数的参数列表为空，执行作用域的this将被视为新函数的thisArg。

### arg1, arg2, ...

    当目标函数被调用时，预先添加到绑定函数的参数列表中的参数。
    ### 返回值：
        返回一个原函数的拷贝，并拥有指定的this值和初始参数。
    
## 示例：

<a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/bind#%E5%88%9B%E5%BB%BA%E7%BB%91%E5%AE%9A%E5%87%BD%E6%95%B0" style="color: rgb(249, 150, 59);">创建绑定函数</a>

bind() 最简单的用法是创建一个函数，不论怎么调用，这个函数都有同样的 this 值。JavaScript新手经常犯的一个错误是将一个方法从对象中拿出来，然后再调用，期望方法中的 this 是原来的对象（比如在回调中传入这个方法）。如果不做特殊处理的话，一般会丢失原来的对象。基于这个函数，用原始的对象创建一个绑定函数，巧妙地解决了这个问题：
``` javascript
    this.x = 9;    // 在浏览器中，this指向全局的 "window" 对象
    var module = {
            x: 81,
            getX: function() { return this.x; }
    };
    
    module.getX(); // 81
    
    var retrieveX = module.getX;
    retrieveX();   
    // 返回9 - 因为函数是在全局作用域中调用的
    
    // 创建一个新函数，把 'this' 绑定到 module 对象
    // 新手可能会将全局变量 x 与 module 的属性 x 混淆
    var boundGetX = retrieveX.bind(module);
    boundGetX(); // 81
```

    
## 偏函数
bind()的另一个最简单的用法是使一个函数拥有预设的初始参数。只要将这些参数（如果有的话）作为bind()的参数写在this后面。当绑定函数被调用时，这些参数会被插入到目标函数的参数列表的开始位置，传递给绑定函数的参数会跟在它们后面。

``` javascript

function list() {
        return Array.prototype.slice.call(arguments);
    }

function addArguments(arg1, arg2) {
        return arg1 + arg2
    }

var list1 = list(1, 2, 3); // [1, 2, 3]

var result1 = addArguments(1, 2); // 3

// 创建一个函数，它拥有预设参数列表。
var leadingThirtysevenList = list.bind(null, 37);

// 创建一个函数，它拥有预设的第一个参数
var addThirtySeven = addArguments.bind(null, 37); 

var list2 = leadingThirtysevenList(); 
// [37]

var list3 = leadingThirtysevenList(1, 2, 3); 
// [37, 1, 2, 3]

var result2 = addThirtySeven(5); 
// 37 + 5 = 42 

var result3 = addThirtySeven(5, 10);
// 37 + 5 = 42 ，第二个参数被忽略
```
## <a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/bind#%E9%85%8D%E5%90%88_setTimeout" style="color: rgb(249, 150, 59);">配合 setTimeout</a>

在默认情况下，使用 <a href="https://developer.mozilla.org/zh-CN/docs/Web/API/Window/setTimeout" title="WindowOrWorkerGlobalScope 混合的 setTimeout()方法设置一个定时器，该定时器在定时器到期后执行一个函数或指定的一段代码。">window.setTimeout()</a> 时，this 关键字会指向 <a href="https://developer.mozilla.org/zh-CN/docs/Web/API/Window" title="The window object represents a window containing a DOM document; the document property points to the DOM document loaded in that window.">window</a> （或global）对象。当类的方法中需要 this 指向类的实例时，你可能需要显式地把 this 绑定到回调函数，就不会丢失该实例的引用。

``` javascript
function LateBloomer() {
    this.petalCount = Math.ceil(Math.random() * 12) + 1;
}

// 在 1 秒钟后声明 bloom
LateBloomer.prototype.bloom = function() {
    window.setTimeout(this.declare.bind(this), 1000);
};

LateBloomer.prototype.declare = function() {
    console.log('I am a beautiful flower with ' +
    this.petalCount + ' petals!');
};

var flower = new LateBloomer();
flower.bloom();  // 一秒钟后, 调用'declare'方法
```
## 快捷调用
在你想要为一个需要特定的 this 值的函数创建一个捷径（shortcut）的时候，bind() 也很好用。
你可以用 <a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/slice" title="The source for this interactive demo is stored in a GitHub repository. If you'd like to contribute to the interactive demo project, please clone https://github.com/mdn/interactive-examples and send us a pull request.">Array.prototype.slice</a> 来将一个类似于数组的对象（array-like object）转换成一个真正的数组，就拿它来举例子吧。你可以简单地这样写：

``` javascript
var slice = Array.prototype.slice;

// ...

slice.apply(arguments);
```

用 bind()可以使这个过程变得简单。在下面这段代码里面，slice 是 <a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/prototype" title="Function.prototype 属性存储了 Function 的原型对象。">Function.prototype</a> 的 <a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/apply" title="apply() 方法调用一个具有给定this值的函数，以及作为一个数组（或类似数组对象）提供的参数。">apply()</a> 方法的绑定函数，并且将 <a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/prototype" title="Array.prototype  属性表示 Array 构造函数的原型，并允许您向所有Array对象添加新的属性和方法。">Array.prototype</a> 的 <a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/slice" title="The source for this interactive demo is stored in a GitHub repository. If you'd like to contribute to the interactive demo project, please clone https://github.com/mdn/interactive-examples and send us a pull request.">slice()</a> 方法作为 this 的值。这意味着我们压根儿用不着上面那个 apply()调用了。

``` javascript
// 与前一段代码的 "slice" 效果相同
var unboundSlice = Array.prototype.slice;
var slice = Function.prototype.apply.bind(unboundSlice);

// ...

slice(arguments);
```
## Polyfill
``` javascript

if (!Function.prototype.bind) {
        Function.prototype.bind = function(oThis) {
        if (typeof this !== 'function') {
            // closest thing possible to the ECMAScript 5
            // internal IsCallable function
            throw new TypeError('Function.prototype.bind - what is trying to be bound is not callable');
        }

    var aArgs   = Array.prototype.slice.call(arguments, 1),
        fToBind = this,
        fNOP    = function() {},
        fBound  = function() {
                // this instanceof fBound === true时,说明返回的fBound被当做new的构造函数调用
                return fToBind.apply(this instanceof fBound
                        ? this
                        : oThis,
                        // 获取调用时(fBound)的传参.bind 返回的函数入参往往是这么传递的
                        aArgs.concat(Array.prototype.slice.call(arguments)));
            };

    // 维护原型关系
    if (this.prototype) {
            // Function.prototype doesn't have a prototype property
            fNOP.prototype = this.prototype; 
        }
    // 下行的代码使fBound.prototype是fNOP的实例,因此
    // 返回的fBound若作为new的构造函数,new生成的新对象作为this传入fBound,新对象的__proto__就是fNOP的实例
    fBound.prototype = new fNOP();

    return fBound;
    };
}
```