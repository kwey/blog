---
title: JS 原型与原型链
tags: 'es6'
categories: 'Javascript'
top_img: '../../img/js.jpg'
---

<a href="https://www.webq.top/article/828" target="_blank">上一篇：继承与原型链</a>
        
        
        
## 一：每个对象都有 __proto__ 属性，但只有函数对象才有 prototype 属性
注：Function.prototype为空函数，没有prototype（箭头函数也没有

``` javascript
function Person() {}

var person1 = new Person();
```

所有的原型对象都会自动获得一个 constructor（构造函数）属性，这个属性（是一个指针）指向 prototype 属性所在的函数（Person）

``` javascript
    person1.constructor == Person
    Person.prototype.constructor == Person
```



结论：原型对象（Person.prototype）是 构造函数（Person）的一个实例。

``` javascript
function Person(){};
console.log(Person.prototype) //Person{}
console.log(typeof Person.prototype) //Object
<span style="color: rgb(249, 150, 59); font-size: x-large;">console.log(typeof Function.prototype) // Function，这个特殊（为空函数
console.log(typeof Object.prototype) // Object
console.log(typeof Function.prototype.prototype) //undefined
```
Function.prototype 为什么是函数对象呢？

``` javascript
var A = new Function ();
Function.prototype = A;
```

## 凡是通过 new Function( ) 产生的对象都是函数对象。因为 A 是函数对象，所以Function.prototype 是函数对象。


所有函数对象proto都指向 Function.prototype，它是一个空函数（Empty function）




## 所有对象的__proto__都指向其构造器的prototype
console.log(Function.prototype.__proto__ === Object.prototype) // true

## 二：原型链

<li>1、person1.__proto__ 是什么？</li><li>
</li>
<li>
2、Person.__proto__ 是什么？</li><li>
</li>
<li>
3、Person.prototype.__proto__ 是什么？</li><li>
</li>
<li>
4、Object.__proto__ 是什么？</li><li>
</li>
<li>
5、Object.prototype__proto__ 是什么？</li><li>
</li>

答案：

第一题：


因为 person1.__proto__ === person1 的构造函数.prototype

因为 person1的构造函数 === Person

所以 person1.__proto__ === Person.prototype




第二题：

因为 Person.__proto__ === Person的构造函数.prototype

因为 Person的构造函数 === Function

所以 Person.__proto__ === Function.prototype




第三题：

Person.prototype 是一个普通对象，我们无需关注它有哪些属性，只要记住它是一个普通对象。

因为一个普通对象的构造函数 === Object

所以 Person.prototype.__proto__ === Object.prototype




第四题，参照第二题，因为 Person 和 Object 一样都是构造函数



第五题：

Object.prototype 对象也有proto属性，但它比较特殊，为 null 。因为 null 处于原型链的顶端，这个只能记住。

Object.prototype.__proto__ === null


拓展：
<a href="https://www.jianshu.com/p/dee9f8b14771" target="_blank">最详尽的 JS 原型与原型链终极详解</a>