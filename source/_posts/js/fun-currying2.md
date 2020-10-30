---
title: 柯里化通用式以及Arrary方法实现
tags: 'Javascript'
categories: 'web'
top_img: '/img/js.jpg'
---
> JS属于解释型语言，在执行过程中顺序执行，但是会分块先预编译然后才执行。因此在JS中存在一种变量提升的现象



``` javascript
// 利用闭包把所有参数存起来var currying = function(fn) {
    var args = [].slice.call(arguments, 1);

    return function() {
        // 主要还是收集所有需要的参数到一个数组中，便于统一计算
        var _args = args.concat([].slice.call(arguments));
        return fn.apply(null, _args);
    }
}

var sum = currying(function() {
    var args = [].slice.call(arguments);
    return args.reduce(function(a, b) {
        return a + b;
    })
}, 10)

console.log(sum(20, 10));  // 40
console.log(sum(10, 5));   // 25
```

补充：
一：map

注：以下全部省略callback前面的function 
``` javascript
array.map(callback(value,index,arrary){
    
},[ options]);
```

callback: 回调支持3个参数，第1个是遍历的数组内容；第2个是对应的数组索引，第3个是数组本身。

options: 如果这第2个可选参数不指定，则使用全局对象代替（在浏览器是为window），严格模式下是undefined

``` javascript

// 回调函数中有三个参数
// 第一个参数表示newArr的每一项，第二个参数表示该项在数组中的索引值
// 第三个表示数组本身
// 除此之外，回调函数中的this，
//当map不存在第二参数时，this指向丢失，
//当存在第二个参数时，指向改参数所设定的对象
var newArr = [1, 2, 3, 4].map(function(item, i, arr) {
    console.log(item, i, arr, this);//this为{a:1}
return item + 1;  // 每一项加1
}, { a: 1 })

console.log(newArr); // [2, 3, 4, 5]
```



``` javascript

Array.prototype._map = function(fn, context) {
    var temp = [];
    if(typeof fn == 'function') {
        var k = 0;
        var len = this.length;
        // 封装for循环过程
        for(; k &lt; len; k++) {
// 将每一项的运算操作丢进fn里，利用call方法指定fn的this指向与具体参数
            temp.push(fn.call(context, this[k], k, this))
        }
} else {
        console.error('TypeError:'+fn+'is not a function.');
    }

// 返回每一项运算结果组成的新数组
return temp;
}

var newArr = [1, 2, 3, 4].map(function(item) {
    return item + 1;
})
// [2, 3, 4, 5]
```
## 二：filter
``` javascript
array.filter(callback(value,index,arrary){

},[ options]);
```

``` javascript

同上；
```

``` javascript

if (typeof Array.prototype.filter != "function") {
    Array.prototype.filter = function (fn, context) {
    var arr = [];
    if (typeof fn === "function") {
        for (var k = 0,len=this.length;k&lt;len; k++) {
            fn.call(context, this[k], k, this)
            &amp;&amp; arr.push(this[k]);
        }
    }
    return arr;
    };
}
```

## 三：forEach

``` javascript
array.forEach(callback(value,index,arrary){
```

``` javascript
},[ options ])
```

同上；

``` javascript
var array = [1, 2, 3];
``` javascript
delete array[1]; // 移除 2
alert(array); // "1,,3"
```

``` javascript

alert(array.length); // but the length is still 3
``` javascript
array.forEach(alert); // 弹出的仅仅是1和3
```

``` javascript

if (typeof Array.prototype.forEach != "function") {
        Array.prototype.forEach = function (fn, context) {
        for (var k = 0, len = this.length; k &lt; len; k++) {
            if (typeof fn === "function"
        &amp;&amp; Object.prototype.hasOwnProperty.call(this,k)
        ) {
            fn.call(context, this[k], k, this);
            }
    }
    };
}
```

## 四：some
``` javascript
array.some(callback(value,index,arrary){
```

``` javascript
},[ options ])
```

同上；
``` javascript

if (typeof Array.prototype.some != "function") {
    Array.prototype.some = function (fn, context) {
    var passed = false;
    if (typeof fn === "function") {
          for (var k = 0, len=this.length; k&lt; len; k++) {
            if (passed === true) break;
        passed = !!fn.call(context, this[k], k,this);
      }
}
return passed;
};
}
```

## 五：every

``` javascript
array.every(callback(value,index,arrary){
```

``` javascript
},[ options ])
```

同上；
``` javascript

if (typeof Array.prototype.every != "function") {
    Array.prototype.every = function (fn, context) {
    var passed = true;
    if (typeof fn === "function") {
        for (var k = 0,len = this.length;k&lt;len; k++){
        if (passed === false) break;
        passed =!!fn.call(context,this[k],k,this);
        }
}
return passed;
};
}
```



## 六：indexOf

``` javascript
array.indexOf(searchElement[, fromIndex])
```

返回整数索引值，如果没有匹配（严格匹配），返回-1. fromIndex可选，表示从这个位置开始搜索，若缺省或格式不合要求，使用默认值0，我在FireFox下测试，发现使用字符串数值也是可以的，例如"3"和3都可以。

``` javascript

if (typeof Array.prototype.indexOf != "function") {
        Array.prototype.indexOf=function(search,from) {
        var index = -1;
        from = from * 1 || 0;
    
        for (var k=0,len=this.length;k &lt; len; k++) {
            if(k>=from &amp;&amp; this[k]===search){
                index = k;
                break;
            }
    }
    return index;
    };
}
```

## lastIndexOf

lastIndexOf方法与indexOf方法类似：

``` javascript
array.lastIndexOf(searchElement[, fromIndex])
```

只是lastIndexOf是从字符串的末尾开始查找，而不是从开头。还有一个不同就是fromIndex的默认值是array.length - 1而不是0.

``` javascript

if (typeof Array.prototype.lastIndexOf!="function") {
        Array.prototype.lastIndexOf=function(search, from) {
        var index = -1, length = this.length;
        from = from * 1 || length - 1;
    
        for (var k = length - 1; k > -1; k-=1) {
            if (k &lt;= from &amp;&amp; this[k] === search) {
                index = k;
                break;
            }
    }
    return index;
    };
}
```

## reduce

``` javascript
array.reduce(callback(prev,value,index,arrary){

```

``` javascript
},[ initialValue ])

```

callback函数接受4个参数：之前值、当前值、索引值以及数组本身。initialValue参数可选，表示初始值。若指定，则当作最初使用的prev值；如果缺省，则使用数组的第一个元素作为prev初始值，同时current往后排一位，相比有initialValue值少一次迭代。

``` javascript

if (typeof Array.prototype.reduce != "function") {
        Array.prototype.reduce=function(callback,init){
        var prev = init, k = 0,len=this.length;
        if (typeof init === "undefined") {
            prev = this[0];
            k = 1;
        }
    
    if (typeof callback === "function") {
        for (k; k &lt; length; k++) {
            this.hasOwnProperty(k)
        &amp;&amp; (prev = callback(prev,this[k],k,this))
    }
    }
    return prev;
    };
}
```

## reduceRight

reduceRight跟reduce相比，用法类似：

``` javascript
array.reduceRight(callback,[ initialValue ])
```

实现上差异在于reduceRight是从数组的末尾开始实现
``` javascript
if (typeof Array.prototype.reduceRight != "function"){
        Array.prototype.reduceRight=function(callback,init) {
        var len=this.length,k = len - 1, prev = init;
        if (typeof init === "undefined") {
            prev = this[length - 1];
            k--;
        }
    if (typeof callback==="function") {
        for (k; k > -1; k-=1) {          
            this.hasOwnProperty(k)
        &amp;&amp; (prev = callback(prev,this[k],k,this));
    }
    }
    return prev;
    };
}
```