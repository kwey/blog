---
title: JS 引擎的执行机制
tags: 'Javascript'
categories: 'web'
top_img: '/img/js.jpg'
---



JSJS的执行机制是：


首先判断JS是同步还是异步，同步就进入主进程，异步就进入event table

异步任务在event table中注册函数，当满足触发条件后，被推入event queue

同步任务进入主线程后一直执行，直到主线程空闲时，才会去event queue中查看是否有可执行的异步任务，如果有就推入主进程中
以上三步循环执行，这就是event loop。


而准确的划分方式是：

macro-task(宏任务)：包括整体代码script，setTimeout，setInterval，setImmediate

micro-task(微任务)：Promise，process.nextTick
![](/img/micro-task.png)

``` javascript
 setTimeout(function(){
     console.log('定时器开始啦')
 });
 


 new Promise(function(resolve){
     console.log('马上执行for循环啦');
     for(var i = 0; i &lt; 10000; i++){
         i == 99 &amp;&amp; resolve();
     }
     
 }).then(function(){
     console.log('执行then函数啦')
 });
 


 console.log('代码执行结束');
 ```
首先执行script下的宏任务，遇到setTimeout,将其放到宏任务的“队列”里

遇到 new Promise直接执行，打印"马上执行for循环啦"

遇到then方法，是微任务，将其放到微任务的“队列”里。

打印 "代码执行结束"

本轮宏任务执行完毕，查看本轮的微任务，发现有一个then方法里的函数，打印"执行then函数啦"

到此,本轮的event loop 全部完成。

下一轮的循环里，先执行一个宏任务，发现宏任务的“队列”里有一个setTimeout里的函数,执行打印"定时器开始啦"
所以最后的执行顺序是： 马上执行for循环啦---代码执行结束---执行then函数啦---定时器开始啦

## setTimeout
这段setTimeout代码什么意思? 我们一般说: 3秒后,会执行setTimeout里的那个函数

``` javascript
 setTimeout(function(){
 
    console.log('执行了')
    
 },3000)
  ```
但是这种说并不严谨，准确的解释是：3秒后，setTimeout里的函数被会推入event queue，而event queue(事件队列)里的任务，只有在主线程空闲时才会执行。

所以只有满足 (1)3秒后 (2)主线程空闲，同时满足时，才会3秒后执行该函数
如果主线程执行内容很多，执行时间超过3秒，比如执行了10秒，那么这个函数只能10秒后执行了。





