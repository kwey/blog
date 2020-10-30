---
title: new 命令
tags: 'Javascript'
categories: 'web'
top_img: '/img/js.jpg'
---

## new命令的原理

使用new命令时，它后面的函数调用就不是正常的调用，而是依次执行下面的步骤。

* 1. 创建一个空对象，作为将要返回的对象实例
* 2. 将这个空对象的原型，指向构造函数的prototype属性
* 3. 将这个空对象赋值给函数内部的this关键字
* 4. 开始执行构造函数内部的代码

> 注意：如果构造函数内部有return语句，而且return后面跟着一个对象，new命令会返回return语句指定的对象；否则，就会不管return语句，返回this对象。

``` javascript
function Car() {
    this.name = 11; 
    return { age: 2 }
}
car1 = new Car()
```
这样是取不到car1里面的name属性，
如果构造函数里面return 的为非对象，依然可以取到name

### new命令简化的内部流程
``` javascript
function _new(
    /* 构造函数 */ 
    constructor(/* 构造函数参数 */ param1) {
        // 将 arguments 对象转为数组
        var args = [].slice.call(arguments);
        // 取出构造函数
        var constructor = args.shift();
        // 创建一个空对象，继承构造函数的 prototype 属性
        var context = Object.create(constructor.prototype);
        // 执行构造函数
        var result = constructor.apply(context, args);
        // 如果返回结果是对象，就直接返回，则返回 context 对象
        return (typeof result === 'object' && result != null) ? result : context)
    }
)

// 实例
var actor = _new(Person, '张三', 28);
```
new.target
函数内部可以使用new.target属性。如果当前函数是new命令调用，new.target指向当前函数，否则为undefined。
``` javascript
function f() {
    console.log(new.target === f);
}
f() // false
new f() // true
```
