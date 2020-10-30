---
title: 继承与原型链
tags: 'Javascript'
categories: 'web'
top_img: '/img/js.jpg'
---
> 对于有基于类的语言经验 (如 Java 或 C++) 的开发人员来说，JavaScript 有点令人困惑，因为它是动态的，并且本身不提供一个class实现。（在 ES2015/ES6 中引入了class关键字，但只是语法糖，JavaScript 仍然是基于原型的）。

<a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Inheritance_and_the_prototype_chain" target="_blank">MDN</a>
        
当谈到继承时，JavaScript 只有一种结构：对象。每个实例对象（object ）都有一个私有属性（称之为__proto__）指向它的原型对象（prototype）。该原型对象也有一个自己的原型对象(__proto__) ，层层向上直到一个对象的原型对象为 null。根据定义，null 没有原型，并作为这个原型链中的最后一个环节。
## 原型链
<img src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAk4AAAHdCAMAAADcnQa3AAAAGXRFWHRTb2Z0d2FyZQBBZG9iZSBJbWFnZVJlYWR5ccllPAAAAyhpVFh0WE1MOmNvbS5hZG9iZS54bXAAAAAAADw/eHBhY2tldCBiZWdpbj0i77u/IiBpZD0iVzVNME1wQ2VoaUh6cmVTek5UY3prYzlkIj8+IDx4OnhtcG1ldGEgeG1sbnM6eD0iYWRvYmU6bnM6bWV0YS8iIHg6eG1wdGs9IkFkb2JlIFhNUCBDb3JlIDUuNi1jMTM4IDc5LjE1OTgyNCwgMjAxNi8wOS8xNC0wMTowOTowMSAgICAgICAgIj4gPHJkZjpSREYgeG1sbnM6cmRmPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5LzAyLzIyLXJkZi1zeW50YXgtbnMjIj4gPHJkZjpEZXNjcmlwdGlvbiByZGY6YWJvdXQ9IiIgeG1sbnM6eG1wPSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvIiB4bWxuczp4bXBNTT0iaHR0cDovL25zLmFkb2JlLmNvbS94YXAvMS4wL21tLyIgeG1sbnM6c3RSZWY9Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC9zVHlwZS9SZXNvdXJjZVJlZiMiIHhtcDpDcmVhdG9yVG9vbD0iQWRvYmUgUGhvdG9zaG9wIENDIDIwMTcgKE1hY2ludG9zaCkiIHhtcE1NOkluc3RhbmNlSUQ9InhtcC5paWQ6OEU3RkM2RjMzOTAxMTFFOUI4QTg5MzRCRUZBMzU4NEUiIHhtcE1NOkRvY3VtZW50SUQ9InhtcC5kaWQ6OEU3RkM2RjQzOTAxMTFFOUI4QTg5MzRCRUZBMzU4NEUiPiA8eG1wTU06RGVyaXZlZEZyb20gc3RSZWY6aW5zdGFuY2VJRD0ieG1wLmlpZDo4RTdGQzZGMTM5MDExMUU5QjhBODkzNEJFRkEzNTg0RSIgc3RSZWY6ZG9jdW1lbnRJRD0ieG1wLmRpZDo4RTdGQzZGMjM5MDExMUU5QjhBODkzNEJFRkEzNTg0RSIvPiA8L3JkZjpEZXNjcmlwdGlvbj4gPC9yZGY6UkRGPiA8L3g6eG1wbWV0YT4gPD94cGFja2V0IGVuZD0iciI/PlKW2cwAAAAMUExURTg4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" style="max-width:100%;">

## 继承：

## 一：基于原型链的继承
``` javascript

function a() {}
function b() {
    b.prototype = new a;
}
```

优点：
1、 简单，一行代码
2、 直接修改b.prototype 不会影响到a.prototype

缺点：
1、为b.prototype增加属性或方法的时候必须在这行代码之后
2、无法实现多继承
3、 修改b.prototype.name.type 会影响到a.prototype
4. 无法传参## 二：借用构造函数
``` javascript

function a() {}
function b() {
   a.apply(this, arguments)
<divcourier new',="" monospace;font-weight:="" normal;font-size:="" 12px;line-height:="" 18px;white-space:="" pre;"=""></divcourier>
}
```

优点：
1、 可以多继承
2、 可以传参

3、 属性共享
缺点：
1、创建的实例只是b的实例，但不是a的示例，相当于把a里面的代码复制到b里面
2、创建的实例无法使用a原型上的属性和方法
3、无法复用，每创建一个实例，都会在a中运行一遍，占用内存## 三：组合式继承
``` javascript

function a() {}
function b() {
    a.apply(this, arguments)
    b.prototype = new a;
    b.constructor = b
}
```

优点：
解决了前两个的缺点
缺点：
1、a被调用了两次
2、创建的实例上存在了一份a里面的属性和方法、原型上也存在了一份## 四：原型式继承
``` javascript

c = {}
d = {}
function a() {}
function object(o){
    function F(){}
    F.prototype = o;
    return new F();
}
一：
a.prototype = c;
d.prototype = new a();

二：
d.__proto__ = c // ie上不好使
三：ES5
<divcourier new',="" monospace;font-weight:="" normal;font-size:="" 12px;line-height:="" 18px;white-space:="" pre;"=""></divcourier>
d = Object.create(c)
```
## 
object.create() 接收两个参数:<ul><li>1、一个用作新对象原型的对象</li><li>2、(可选的)一个为新对象定义额外属性的对象</li><li>
</li></ul>## 

object.create() 只有一个参数时功能与上述object方法相同, 它的第二个参数与Object.defineProperties()方法的第二个参数格式相同: 每个属性都是通过自己的描述符定义的.以这种方式指定的任何属性都会覆盖原型对象上的同名属性.例如:


``` javascript
var person = {
    name : "Van"
};
var anotherPerson = Object.create(person, {
    name : {
        value : "Louis"
    }
});
alert(anotherPerson.name);//"Louis"
```

目前支持 Object.create() 的浏览器有 IE9+, Firefox 4+, Safari 5+, Opera 12+ 和 Chrome.

## 五：寄生式继承
同四差不多
``` javascript

function object(o) {
    function F(){}
    F.prototype = o;
    return new F();
}

function c(original) {
    // 通过调用函数创建一个新对象
    var clone = object(original);  
    // 以某种方式来增强这个对象
    clone.sayHi = function() {
        alert("hi");
    }
    return clone;
}
```
## 六：组合寄生式继承（完美继承）
``` javascript
    
function a() {}
function b() {
        a.apply(this, arguments);
    }
// b.prototype.__proto__ = a.prototype
function object(o) {
        function F(){}
    F.prototype = o;
    return new F();
}
const f = object(a.prototype) // 创建一个空对象，让它的原型指向a的原型
f.constructor = b
b.prototype = f
```

