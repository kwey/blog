---
title: 函数声明、函数表达式、匿名函数
tags: 'Javascript'
categories: 'web'
top_img: '/img/js.jpg'
---
> 一个函数是可以通过外部代码调用的一个“子程序”（或在递归的情况下由内部函数调用）。像程序本身一样，一个函数由称为函数体的一系列语句组成。值可以传递给一个函数，函数将返回一个值。

 ## 函数声明：

function fnName () {…};使用function关键字声明一个函数，再指定一个函数名，叫函数声明。

## 函数表达式 :
var fnName = function () {…};使用function关键字声明一个函数，但未给函数命名，最后将匿名函数赋予一个变量，叫函数表达式，这是最常见的函数表达式语法形式。
## 匿名函数：
function () {}; 使用function关键字声明一个函数，但未给函数命名，所以叫匿名函数，匿名函数属于函数表达式，匿名函数有很多作用，赋予一个变量则创建函数，赋予一个事件则成为事件处理程序或创建闭包等等。


## 函数声明和函数表达式不同之处在于
一、Javascript引擎在解析javascript代码时会‘函数声明提升'（Function declaration Hoisting）当前执行环境（作用域）上的函数声明，而函数表达式必须等到Javascirtp引擎执行到它所在行时，才会从上而下一行一行地解析函数表达式

二、函数表达式后面可以加括号立即调用该函数，函数声明不可以，只能以fnName()形式调用 



（）、！、+、-、=等运算符，都将函数声明转换成函数表达式，消除了javascript引擎识别函数表达式和函数声明的歧义，告诉javascript引擎这是一个函数表达式，不是函数声明，可以在后面加括号，并立即执行函数的代码。

    注意：
``` javascript
var Vehicle = function aassss() {};
var Vehicle2 = function () {};
console.log(Vehicle.name); // aassss
console.log(Vehicle2.name); // Vehicle2
aassss() // 会报错,aassss只能在他的函数体里面调用（函数表达式中的函数名会被忽略）
```

## 函数名和函数的变量存在着差别。函数名不能被改变，但函数的变量却能够被再分配。函数名只能在函数体内使用。倘若在函数体外使用函数名将会导致错误（如果函数之前是通过一个var语句声明的则是undefined）。例如：
``` javascript
var y = function x() {};
alert(x); // throws an error
却可以这样

var y = function x() {
    console.log(x)
};
```


    
### 使用用 'new Function'定义的函数没有函数名。a
``` javascript

console.log(new Function())会输出
function anonymous() {}
    
    
var foo = new Function("alert(anonymous);"); 
foo(); // error, anonymous is not defined
```

