---
title: JavaScript 数据类型转换
tags: 'Javascript'
categories: 'web'
top_img: '/img/js.jpg'
---
> JS属于解释型语言，在执行过程中顺序执行，但是会分块先预编译然后才执行。因此在JS中存在一种变量提升的现象


## 强制转换
强制转换主要指使用Number、String和Boolean三个构造函数，手动将各种类型的值，转换成数字、字符串或者布尔值。

``` javascript

// 数值：转换后还是原来的值
Number(324) // 324

//字符串：如果可以被解析为数值，则转换为相应的数值
Number('324') // 324

// 字符串：如果不可以被解析为数值，返回NaN
Number('324abc') // NaN

// 空字符串转为0
Number('') // 0

// 布尔值：true 转成1，false 转成0
Number(true) // 1
Number(false) // 0

// undefined：转成 NaN
Number(undefined) // NaN

// null：转成0
Number(null) // 0
parseInt逐个解析字符，而Number函数整体转换字符串的类型。
parseInt('42 cats') // 42
Number('42 cats') // NaN

Number函数会自动过滤一个字符串前导和后缀的空格。Number('\\t\\v\\r12.34\
') // 12.34

Number方法的参数是对象时，将返回NaN
除非是包含单个数值的数组
Number({a: 1}) // NaN
Number([1, 2, 3]) // NaN
Number([5]) // 5

```
之所以会这样，是因为Number背后的转换规则比较复杂。

1、调用对象自身的valueOf方法。
    如果返回原始类型的值，则直接对该值使用Number函数，

    不再进行后续步骤。


2、如果valueOf方法返回的还是对象

    则改为调用对象自身的toString方法。

    如果toString方法返回原始类型的值，
    
    则对该值使用Number函数，不再进行后续步骤。


3、如果toString方法返回的是对象，就报错。

    使用String函数，可以将任意类型的值转化成字符串。转换规则如下。
（1）原始类型值的转换规则

* 数值：转为相应的字符串。
* 字符串：转换后还是原来的值。
* 布尔值：true转为"true"，false转为"false"。
* undefined：转为"undefined"。
* null：转为"null"。

2）对象的转换规则

String方法的参数如果是对象，返回一个类型字符串；如果是数组，返回该数组的字符串形式
``` javascript
String({a: 1}) // "[object Object]"

String([1, 2, 3]) // "1,2,3"
```

String方法背后的转换规则，与Number方法基本相同，只是互换了valueOf方法和toString方法的执行顺序
先调用对象自身的toString方法。如果返回原始类型的值，则对该值使用String函数，不再进行以下步骤。 

如果toString方法返回的是对象，再调用原对象的valueOf方法。如果valueOf方法返回原始类型的值，则对该值使用String函数，不再进行以下步骤。
如果valueOf方法返回的是对象，就报错。

## Boolean()
使用Boolean函数，可以将任意类型的变量转为布尔值。

它的转换规则相对简单：除了以下六个值的转换结果为false，其他的值全部为true。
```
undefined
null
-0
0或+0
NaN
''（空字符串）
```
## 自动转换
自动转换，它是以强制转换为基础的。
遇到以下三种情况时，JavaScript会自动转换数据类型，即转换是自动完成的，对用户不可见。
``` javascript

//不同类型的数据互相运算
123 + 'abc' // "123abc"

//对非布尔值类型的数据求布尔值
if ('abc') {
        console.log('hello')
    }  // "hello"
    
    //对非数值类型的数据使用一元运算符
//（即“+”和“-”）
+ {foo: 'bar'} // NaN
- [1, 2, 3] // NaN
```

自动转换的规则是这样的：预期什么类型的值，就调用该类型的转换函数。比如，某个位置预期为字符串，就调用String函数进行转换。如果该位置即可以是字符串，也可能是数值，那么默认转为数值。
由于自动转换具有不确定性，而且不易除错，建议在预期为布尔值、数值、字符串的地方，全部使用Boolean、Number和String函数进行显式转换。
## 自动转换为字符串
``` javascript

    '5' + 1 // '51'
    '5' + true // "5true"
    '5' + false // "5false"
    '5' + {} // "5[object Object]"
    '5' + [] // "5"
    '5' + function (){} // "5function (){}"
    '5' + undefined // "5undefined"
    '5' + null // "5null"
```
## 自动转换为数值
``` javascript
    '5' - '2' // 3
    '5' * '2' // 10
    true - 1  // 0
    false - 1 // -1
    '1' - 1   // 0
    '5' * []    // 0
    false / '5' // 0
    'abc' - 1   // NaN
    上面代码中，运算符两侧的运算子，
    都被转成了数值。
    一元运算符也会把运算子转成数值。
    +'abc' // NaN
    -'abc' // NaN
    +true // 1
    -false // 0
```
