---
title: JavaScipt中队列的实现
tags: 'Javascript'
categories: 'web'
top_img: '/img/js.jpg'
---
> 队列与栈是很相像的数据结构，不同之处在于队列是是先进先出(FIFO:First In First Out)的。


<ul><li>
队列需要有如下的方法:<ul><li>
enqueue(element(s)): 向队列尾部添加几个项</li><li>
dequeue(): 移除队列的第一项(也就是排在最前面的项)</li><li>
front(): 返回队列的第一个元素，也就是最新添加的那个</li><li><ul><li>
isAmpty: 检查队列是否为空，为空则返回true</li><li>
clear: 移除队列中所有元素</li><li>
size: 返回队列中元素个数。</li><li>
print: 以字符串显示队列中所有内容</li></ul></li></ul></li></ul>


``` javascript
/**
* 队列构造函数
*/

/**
* 队列构造函数
*/
function Queue() {

    /**
    * 用数组来模拟队列
    * @type {Array}
    */
    var items = [];

    /**
    * 将元素推入队列
    * @param  {Any} ele 要推入队列的元素
    */
    this.enqueue = function(ele) {
        items.push(ele);
    };

    /**
    * 将队列中第一个元素弹出
    * @return {Any} 返回被弹出的元素
    */
    this.dequeue = function() {
        return items.shift()
    };

    /**
    * 查看队列的第一个元素
    * @return {Any} 返回队列中第一个元素
    */
    this.front = function() {
        return items[0];
    };

    /**
    * 确定队列是否为空
    * @return {Boolean} 若队列为空则返回true,不为空则返回false
    */
    this.isAmpty = function() {
        return items.length === 0
    };

    /**
    * 返回队列的长度
    * @return {Number} 队列的长度
    */
    this.size = function() {
        return items.length;
    };

    /**
    * 清空队列中所有内容
    */
    this.clear = function() {
        items = [];
    };

    /**
    * 以字符串显示队列中所有内容
    */
    this.print = function() {
        console.log(items.toString());
    };
}

```


## 应用

``` javascript
/**
* 队列构造函数
*/
function Queue() {

/**
    * 击鼓传花的小游戏
    * @param  {Array} nameList 参与人员列表
    * @param  {Number} num      在循环中要被弹出的位置
    * @return {String}          返回赢家(也就是最后活下来的那个)
    */
    function hotPotato(nameList, num) {
        var queue = new Queue();

        for (var i = 0; i < nameList.length; i++) {
            queue.enqueue(nameList[i]);
        }

    var eliminated = '';

    while (queue.size() > 1) {
        for (var i = 0; i < num; i++) {
            queue.enqueue(queue.dequeue());
        }

        eliminated = queue.dequeue();
        console.log(eliminated + " Get out!")
    }

    return queue.dequeue()
}

var nameList = ['小明', '小红', '小王', '小绿']
```