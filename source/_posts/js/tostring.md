---
title: toString方法和valueOf方法以及Symbol.toPrimitive
tags: 'Javascript'
categories: 'web'
top_img: '../../img/js.jpg'
---

> 每个对象都有一个toString()方法和valueOf方法，其中toString()方法返回一个表示该对象的字符串，valueOf方法返回该对象的原始值。


## 对于值类型数据(又叫基本类型)场景下，toString及valueOf方法的使用

toString方法对于值类型数据使用而言，其效果相当于类型转换，将原类型转为字符串。

valueOf方法对于值类型数据使用而言，其效果将相当于返回原数据。 

## 复合对象类型数据使用toString及valueOf方法
``` javascript
var test = { 
    i: 10, 
    toString: function() { 
        console.log('toString'); 
        return this.i; 
    }, 
    valueOf: function() { 
        console.log('valueOf'); 
        return this.i; 
    } 
} 
alert(test);// 10 toString 
alert(+test); // 10 valueOf 
alert(''+test); // 10 valueOf 
alert(String(test)); // 10 toString 
alert(Number(test)); // 10 valueOf 
alert(test == '10'); // true valueOf 
alert(test === '10'); // false  不会隐式转换
```
总结： 
* 在进行强转字符串类型时将优先调用toString方法，强转为数字时优先调用valueOf。
* 在有运算操作符的情况下，valueOf的优先级高于toString。

这两个方法一般是交由js去隐式调用，以满足不同的运算情况。 
>在数值运算里，会优先调用valueOf()，如 a + b; 

>在字符串运算里，会优先调用toString(),如alert(c).

null和undefined不是伪对象,null和undefined调用toString()方法会报错

但是String()可以将null和undefined转换为字符串

## Symbol.toPrimitive

对象的Symbol.toPrimitive属性。这是定义在Symbol对象上的一个属性,ES6把这个属性暴露出来，可以在对象上自定义。该对象被转化为原始类型的值时，`会调用这个办法，返回该对象对应的原始类型值。`

Symbol.toPrimitive被调用时,会接受一个字符串参数，表示当前运算的模式，有三种模式。
* number:该场合需要转成数值
* string:该场合需要转成字符串
* default:该场合可以转成数值，也可以转成字符串。

当对象发生到基本类型值的转换时，会按照下面的逻辑调用对象上的方法：
* 如果存在，调用 `obj[Symbol.toPrimitive](hint)`；
* 否则，如果 hint 取值是 "string"：
  
  无论是否存在，调用 obj.toString() 和 obj.valueOf()。
* 否则（也就是 hint 取值是 "number" 或 "default" 的情况）：
  无论是否存在，调用 obj.valueOf() 和 obj.toString()。
  
  
### 确定 hint

我们提到了 ToPrimitive 算法中用到的 hint 参数，那怎样确定一次运算场景下的 hint 取值是什么呢？很简单----新建一个对象，打印各个运算场景下的 hint 值：
``` javascript
let user = {
    name: "John",
    money: 1000,

    [Symbol.toPrimitive](hint) {
        console.log(`hint: ${hint}`);
    }
};

alert(user) // hint: string 
2*user // hint: number
+user // hint: number
user + 500 // hint: default
String(user) // hint: string
```


注意：
* Symbol.toPrimitive 和 toString 方法的返回值必须是基本类型值。
* valueOf 方法除了可以返回基本类型值，也可以返回其他类型值。

当我们创建一个普通对象时（{} 或 new Object() 的方式等），对象上是不具备 [Symbol.toPrimitive] （方法）属性的。所以，对于普通对象的到基本类型值的运算，一般按照具体场景：


> hint 值为 "string" 时，先调用 toString，toString 如果返回一个基本类型值了，则返回、终止运算；否则接着调用 valueOf 方法。

> 否则，先调用 valueOf，valueOf 如果返回一个基本类型值了，则返回、终止运算；否则接着调用 toString 方法。