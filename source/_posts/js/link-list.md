---
title: 单向链表、循环链表、双向链表
tags: 'Javascript'
categories: 'web'
top_img: '/img/js.jpg'
---
> 链表是一种常见的数据结构，也属于线性表，但不会按线性的顺序来储存数据。而是在每一个节点中，储存了下一个节点的指针。


## 使用链表结构可以克服数组需要预先知道数据大小的缺点，链表结构可以充分利用计算机内存空间，实现灵活的内存动态管理。

## 单向链表
链表中最简单的形式就是单向链表，链表中的节点都包含两个部分，第一部分储存着自身信息，第二部分则储存有指向下一节点的指针。最后一个节点则指向NULL，

单向链表需要有如下的方法:
<ul><li>
append(element): 添加元素到链表尾部</li><li>
insert(position,element): 向单向链表中某个位置插入元素</li><li>
indexOf(element): 寻找某个元素在单向链表中的位置</li><li>
remove(element): 移除给定的元素</li><li>
removeAt(position): 移除单向链表中某个位置的元素</li><li>
getHead(): 获取单向链表的头部</li><li>
isAmpty(): 检查单向链表是否为空，为空则返回true</li><li>
toString(): 将链表所有内容以字符串输出</li><li>
size(): 返回单向链表长度</li></ul>


``` javascript

/**
    * 单向链表构造函数
    */
function LinkedList() {
    
    /**
    * 单向链表中节点的构造函数
    * @param {Any} element 要传入链表的节点
    */
    var Node = function(element) {
        this.element = element;
        this.next = null;
    }

    //单向链表的长度
    var length = 0;
    //单向链表的头结点，初始化为NULL
    var head = null;


    /**
    * 向单向链表尾部添加元素
    * @param  {Any} element 要加入链表的节点
    */
    this.append = function(element) {
        var node = new Node(element);
        var current;
    
        if (head == null) {
            head = node;
        } else {
            // 当前项等于链表头部元素.
            // while循环到最后一个，从而将该节点加入链表尾部。
            current = head;
            // 当next为null时，判定为false。退出循环。
            while (current.next) {
            current = current.next;
            }
            current.next = node;
        }
        length++;
    };

    /**
    * 移除单向链表中某一个元素
    * @param  {Number} position 要移除元素的位置
    * @return {Any}          移除成功返回被移除的元素，不成功则返回NULL
    */
    this.removeAt = function(position) {
        if (position > -1 && position < length) {
            var current = head;
            var previous;
            var index = 0;
    
            if (position == 0) {
            // 因为之前head指向第一个元素，现在把head修改为指向第二个元素。
            // 核心概念在于链表前后全靠指针链接，而非数组一般。
            // 所以只需要改变head的元素。
            head = current.next;
            } else {
            while (index++ < position) {
                // previous指要操作元素位置之前的那个元素，current表示之后的那个元素。
                previous = current;
                current = current.next;
            }

        previous.next = current.next;
        }

        length--;

        return current.element;
    } else {
            return null;
        }
    };

    /**
    * 向单向链表中插入某个元素
    * @param  {Number} position 要插入的位置
    * @param  {Any} element  要插入的元素
    * @return {Boolean}          插入成功返回true，失败返回false
    */
    this.insert = function(position, element) {
        if (position >= 0 && position <= length) {
            var node = new Node(element);
            var current = head;
            var previous;
            var index = 0;
    
            if (position == 0) {
            node.next = current;
            head = node;
            } else {
                while (index++ < position) {
                    previous = current;
                    current = current.next;
                }

                previous.next = node;
                node.next = current;
            }

            length++;
            return true;
        } else {
            return false;
        }
    };

    /**
    * 将链表所有内容以字符串输出
    * @return {String} 要输出的字符串
    */
    this.toString = function() {
        var current = head;
        var string = '';
    
        while (current) {
            string += current.element;
            current = current.next;
        }
        return string;
    };

    /**
    * 寻找某个元素在单向链表中的位置
    * @param  {Any} element 要寻找的元素
    * @return {Number}         返回值>=0则代表找到相应位置
    */
    this.indexOf = function(element) {
        var current = head;
        var index = 0;
    
        while (current) {
            if (element === current.element) {
                return index;
            }
            index++;
            current = current.next;
        }

    return -1;
    };

    /**
    * 移除给定的元素
    * @param  {Any} element 要移除的元素
    * @return {Number}         返回值>=0表示移除成功
    */
    this.remove = function(element) {
        var index = this.indexOf(element);
        return this.removeAt(index);
    };

    /**
    * 判断单向链表是否为空
    * @return {Boolean} 为空则返回true，不为空则返回false
    */
    this.isAmpty = function() {
        return length === 0
    };

    /**
    * 返回单向链表长度
    * @return {Number} 单向链表的长度
    */
    this.size = function() {
        return length;
    };

    /**
    * 获取单向链表的头部
    * @return {Any} 单向链表的头部
    */
    this.getHead = function() {
        return head;
    }
}
```

## 循环链表

循环链表和单链表相似，节点类型都是一样，唯一的区别是，在创建循环链表的时候，让其头节点的 next 属性执行它本身，即

## 双向链表
双向链表与单向链表很是相像。在单向链表中，只有指向下一个节点的链接。但在双向链表中，还有指向上一个节点的链接，是双向的。

双向链表需要有如下的方法:<ul><li>
append(element): 添加元素到双向链表尾部</li><li>
insert(position,element): 向双向链表中某个位置插入元素</li><li>
removeAt(position): 移除双向链表中某个位置的元素</li><li>
showHead(): 获取双向链表的头部</li><li>
showLength(): 获取双向链表长度</li><li>
showTail(): 获取双向链表尾部</li></ul>


``` javascript

/**
* 双向链表的构造函数
*/
function DoublyLinkedList() {
    
    /**
    * 双向链表中节点的构造函数
    * @param {Any} element 要传入链表的元素
    */
    var Node = function(element) {
        this.element = element;
        this.prev = null;
        this.next = null;
    }

    //双向链表的长度
    var length = 0;
    //双向链表的头结点，初始化为NULL
    var head = null;
    //双向链表的尾结点，初始化为NULL
    var tail = null;

    /**
    * 向链表尾部添加元素
    * @param  {Any} element 要加入链表的节点
    * @return {Any}         加入链表的节点
    */
    this.append = function(element) {
        var node = new Node(element);
    
        if (head === null) {
            head = node;
            tail = node;
        } else {
            var previous;
            var current = head;
    
            while (current.next) {
                current = current.next;
            }

            current.next = node;
            node.prev = current;
            tail = node;
        }

        length++;
        return node;
    };

    /**
    * 向链表中插入某个元素
    * @param  {Number} position 要插入的位置
    * @return {Boolean}         插入成功返回true，失败返回false
    */
    this.insert = function(position, element) {
        if (position >= 0 && position <= length) {
    
            var node = new Node(element);
            var index = 0;
            var previous;
            var current = head;
    
            if (position === 0) {
    
                if (head === null) {
                    head = node;
                    tail = node;
                } else {
                    current.prev = node;
                    node.next = current;
                    head = node;
                }
            } else if (position === length) {
        
                current = tail;
                current.next = node;
                node.prev = current;
                tail = node;
            } else {
                while (index++ < position) {
                    previous = current;
                    current = current.next;
                }

                previous.next = node;
                node.prev = previous;
                current.prev = node;
                node.next = current;
            }

            length++;
            return true;
        } else {
            return false;
        }
    };

    /**
    * 移除链表中某一个元素
    * @param  {Number} position 要移除元素的位置
    * @return {Any}             移除成功返回被移除的元素，不成功则返回false
    */
    this.removeAt = function(position) {
        if (position > -1 && position < length) {
            var current = head;
            var index = 0;
            var previous;
    
            if (position === 0) {
                head = current.next;
        
                if (length === 1) {
                    tail = null;
                    head.prev = null;
                }
            } else if (position === length - 1) {
                current = tail;
                tail = current.prev;
                tail.next = null;
            } else {
                while (index++ < position) {
                    previous = current.prev;
                    current = current.next;
                }
                previous.next = current.next;
                current.next.prev = previous;
            }

            length--;
            return current.element;
        } else {
                return false;
            }
        };

    /**
    * 获取链表的头部
    * @return {Any} 链表的头部
    */
    this.showHead = function() {
        return head;
    };

    /**
    * 获取链表长度
    * @return {Number} 链表长度
    */
    this.showLength = function() {
        return length;
    };

    /**
    * 获取链表尾部
    * @return {Any} 链表尾部
    */
    this.showTail = function() {
        return tail;
    }
};
```

```