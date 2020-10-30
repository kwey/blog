---
title: JavaScript 柯里化
tags: 'Javascript'
categories: 'web'
top_img: '/img/js.jpg'
---
> 柯里化（英语：Currying），又称为部分求值，是把接受多个参数的函数变换成接受一个单一参数（最初函数的第一个参数）的函数，并且返回一个新的函数的技术，新函数接受余下参数并返回运算结果。

实现一个函数，运算结果可以满足如下预期结果：
        
add(1)(2) // 3

add(1, 2, 3)(10) // 16

add(1)(2)(3)(4)(5) // 15

``` javascript

function add () {
    var args = Array.prototype.slice.call(arguments);
    
    var fn = function () {
        var arg_fn = Array.prototype.slice.call(arguments);
        return add.apply(null, args.concat(arg_fn));
    }
fn.valueOf = function () {
        return args.reduce(function(a, b) {
            return a + b;
        })
}
return fn;
}
```

每次调用返回的都是fn函数，下一次的调用会吧上一次的参数和这一次的参数拼接起来传给add函数、并调用返回fn函数，最后通过隐式数据类型转换调用fn的valueOf方法取出所有参数的和


## Object.prototype.valueOf()
JavaScript调用valueOf方法将对象转换为原始值。当遇到要预期的原始值的对象时，JavaScript会自动调用它。

默认情况下，valueOf方法由<a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object" title="Object 构造函数创建一个对象包装器。">Object</a>后面的每个对象继承。 每个内置的核心对象都会覆盖此方法以返回适当的值。如果对象没有原始值，则valueOf将返回对象本身。
JavaScript的许多内置对象都重写了该函数，以实现更适合自身的功能需要。因此，不同类型对象的valueOf()方法的返回值和返回值类型均可能不同。<table><tbody><tr><td>Array</td><td>返回数组对象本身。</td></tr><tr><td>Boolean</td><td>布尔值。</td></tr><tr><td>Date</td><td>存储的时间是从 1970 年 1 月 1 日午夜开始计的毫秒数 UTC。</td></tr><tr><td>Function</td><td>函数本身。</td></tr><tr><td>Number</td><td>数字值。</td></tr><tr><td>Object</td><td>对象本身。这是默认情况。</td></tr><tr><td>String</td><td>字符串值。</td></tr><tr><td> </td><td>
</td></tr></tbody></table>


## Object.prototype.toString()
toString() 方法返回一个表示该对象的字符串。

JavaScript的许多内置对象都重写了该函数，以实现更适合自身的功能需要。

每个对象都有一个toString()方法，当该对象被表示为一个文本值时，或者一个对象以预期的字符串方式引用时自动调用。默认情况下，toString()方法被每个Object对象继承。如果此方法在自定义对象中未被覆盖，toString() 返回 "[object type]"，其中type是对象的类型。

``` javascript
var toString = Object.prototype.toString;

toString.call(new Date); // [object Date]
toString.call(new String); // [object String]
toString.call(Math); // [object Math]

//Since JavaScript 1.8.5
toString.call(undefined); // [object Undefined]
toString.call(null); // [object Null]
```



3 种主要的原始类型 Boolean 值、数字和字符串(伪对象)都有 toString() 方法，可以把它们的值转换成字符串
<table><tbody><tr><td>Array</td><td>[1,2] >'1,2'</td></tr><tr><td>Boolean</td><td>false > 'false'</td></tr><tr><td>Date</td><td>
``` javascript
Sat Mar 09 2019 21:11:16 GMT+0800 (中国标准时间)
```
</td></tr><tr><td>Function</td><td>
"function Object() { [native code] }"</td></tr><tr><td>Number</td><td>默认模式和基模式
</td></tr><tr><td>Object</td><td>"function Object() { [native code] }"
</td></tr><tr><td>String</td><td>字符串值。</td></tr><tr><td> </td><td>
</td></tr></tbody></table>Number 类型的 toString() 方法比较特殊，它有两种模式，即<默认模式和基模式。采用默认模式，toString() 方法只是用相应的字符串输出数字值（无论是整数、浮点数还是科学计数法）

注释：在默认模式中，无论最初采用什么表示法声明数字，Number 类型的 toString() 方法返回的都是数字的十进制表示。因此，以八进制或十六进制字面量形式声明的数字输出的都是十进制形式的。

采用 Number 类型的 toString() 方法的基模式，可以用不同的基输出数字，例如二进制的基是 2，八进制的基是 8，十六进制的基是 16。

基只是要转换成的基数的另一种加法而已，它是 toString() 方法的参数：

``` javascript
var iNum = 10;
alert(iNum.toString(2));\t//输出 "1010"
alert(iNum.toString(8));\t//输出 "12"
alert(iNum.toString(16));\t//输出 "A"

```

在前面的示例中，以 3 种不同的形式输出了数字 10，即二进制形式、八进制形式和十六进制形式。HTML 采用十六进制表示每种颜色，在 HTML 中处理数字时这种功能非常有用。

注释：对数字调用 toString(10) 与调用 toString() 相同，它们返回的都是该数字的十进制形式。
产生随机字符串：

Math.random().toString(36)

"0.562buo5yjjy""

