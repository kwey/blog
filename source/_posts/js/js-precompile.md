---
title: JavaScript预编译原理分析
tags: 'es6'
categories: 'Javascript'
top_img: '../../img/js.jpg'
---

JavaScript运行三部曲
脚本执行js引擎都做了什么呢？

1、语法分析
2、预编译
3、解释执行

在执行代码前，还有两个步骤 
语法分析很简单，就是引擎检查你的代码有没有什么低级的语法错误 
解释执行顾名思义便是执行代码了 
预编译简单理解就是在内存中开辟一些空间，存放一些变量与函数 
理解了预编译对大家理解作用域同样有帮助


JS预编译实例
举例前，先来思考一下这几个概念：

变量声明 var…
函数声明 function…
```html

<script>
var a = 1;// 变量声明
function b(y){//函数声明
        var x = 1;
        console.log('so easy');
    };
var c = function(){
        //是变量声明而不是函数声明！！
            //...
        }
        b(100);
        </script>

        <script>
var d = 0;
</script>
```


让我们看看引擎对这段代码做了什么吧

页面产生便创建了GO全局对象（Global Object）（也就是大家熟悉的window对象）
第一个脚本文件加载
脚本加载完毕后，分析语法是否合法
开始预编译 
查找变量声明，作为GO属性，值赋予undefined
查找函数声明，作为GO属性，值赋予函数体
```javascript

//伪代码
// GO
window = {
    //页面加载创建GO同时，创建了document、navigator、screen等等属性，此处省略
    a: undefined,
    c: undefined，
    b: function(y){
        var x = 1;
        console.log('so easy');
    }
}
```

解释执行代码（直到执行函数b）
```javascript

//伪代码
// GO
window = {
    //变量随着执行流得到初始化
    a: 1,
    c: function(){
        //...
    },
    b: function(y){
        var x = 1;
        console.log('so easy');
    }
}
```

执行函数b之前，发生预编译 
创建AO活动对象（Active Object）
查找形参和变量声明，值赋予undefined
实参值赋给形参
查找函数声明，值赋予函数体
```javascript

//伪代码
AO = {
    //创建AO同时，创建了arguments等等属性，此处省略
    y: 100,
    x: undefined
}
```


解释执行函数中代码
第一个脚本文件执行完毕，加载第二个脚本文件
第二个脚本文件加载完毕后，进行语法分析
语法分析完毕，开始预编译 
重复最开始的预编译步骤……
大家要注意， 
预编译阶段发生变量声明和函数声明，没有初始化行为（赋值），匿名函数不参与预编译 
只有在解释执行阶段才会进行变量初始化 
嗯~最后收一下尾

总结
预编译(函数执行前)※ 
1. 创建AO对象（Active Object） 
2. 查找函数形参及函数内变量声明，形参名及变量名作为AO对象的属性，值为undefined 
3. 实参形参相统一，实参值赋给形参 
4. 查找函数声明，函数名作为AO对象的属性，值为函数引用

预编译(脚本代码块script执行前) 
1. 查找全局变量声明（包括隐式全局变量声明，省略var声明），变量名作全局对象的属性，值为undefined 
3. 查找函数声明，函数名作为全局对象的属性，值为函数引用
```javascript
function test(a) {
    console.log(a);
    var a = 1
    console.log(a);
    function a(params) { }
    console.log(a);
    var b = function () {}
    console.log(d);
    function d() { }
}
test(2)
// AO activation object
// 1. 寻找形参和变量声明
// 2. 实参赋值给形参
// 3. 寻找函数声明，赋值
// 4. 执行
// AO = {
    //     a: undefined ->
    //         2 ->
    //         function a(params) { } ->
//         1
//     b: undefined ->
//         function () {}
//     d: function () {}

// }
```

![](/img/js-pre.jpg)