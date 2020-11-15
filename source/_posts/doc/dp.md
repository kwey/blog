---
title: 设计模式
top_img: '/img/hy.jpeg'
cover: '/img/hy.jpeg'
---

### 设计模式

设计模式（Design pattern）代表了最佳的实践，是一套被反复使用的、多数人知晓的、经过分类编目的、代码设计经验的总结。使用设计模式是为了重用代码、让代码更容易被他人理解、保证代码可靠性。 
<h3 id="a1">单例模式</h3>

单例就是保证一个类只有一个实例，实现的方法一般是先判断实例存在与否，如果存在直接返回，如果不存在就创建了再返回，这就确保了一个类只有一个实例对象。在JavaScript里，单例作为一个命名空间提供者，从全局命名空间里提供一个唯一的访问点来访问该对象。

应用场景：

创建唯一的浮窗(闭包)
``` javascript
const getSingle = function( fn ){
   const result;
   return function(){
       return result || ( result = fn .apply(this, arguments ) );
   } 
};
const createLoginLayer = function(){

    const div = document.createElement( 'div' );
    div.innerHTML = '我是登录浮窗';
    div.style.display = 'none';  
    document.body.appendChild( div );

    return div;
};
const createSingleLoginLayer = getSingle( createLoginLayer );

document.getElementById( 'loginBtn' ).onclick = function(){ 
    const loginLayer = createSingleLoginLayer(); 
    loginLayer.style.display = 'block';
};
```
<h3 id="a2">策略模式</h3>

策略模式 （Strategy Pattern）又称政策模式，其定义一系列的算法，把它们一个个封装起来，并且使它们可以互相替换。封装的策略算法一般是独立的，策略模式根据输入来调整采用哪个算法。关键是策略的实现和使用分离。

实例：（螺丝刀与螺丝刀头、车与轮胎）

在这些场景中，有以下特点：

螺丝刀头/轮胎（策略）之间相互独立，但又可以相互替换；

螺丝刀/车（封装上下文）可以根据需要的不同选用不同的策略；

应用场景

奖金计算，绩效为 S 的人年 终奖有 4 倍工资，绩效为 A 的人年终奖有 3 倍工资，而绩效为 B 的人年终奖是 2 倍工资
``` javascript
const calculateBonus = (() => {
    const strategies = {
        "S": function( salary ){
            return salary * 4;
        },
        "A": function( salary ){ 
            return salary * 3;
        },
        "B": function( salary ){
            return salary * 2;
        }
    };

    return {
        get: (level, salary) => {
            return strategies[level] && strategies[level](salary)
        },
        set: (level, fn) => {
            strategies[level] = strategies[level] || fn
        }
    }

})()

calculateBonus.set( 'C', (salary) => {
    return salary * 1
} );

calculateBonus.get( 'S', 20000 );// 输出:80000
calculateBonus.get( 'A', 10000 );// 输出:30000
```

策略模式的优缺点

策略模式将算法的实现和使用拆分，这个特点带来了很多优点：

1、策略之间相互独立，但策略可以自由切换，这个策略模式的特点给策略模式带来很多灵活性，也提高了策略的复用率；

2、如果不采用策略模式，那么在选策略时一般会采用多重的条件判断，采用策略模式可以避免多重条件判断，增加可维护性；

3、可扩展性好，策略可以很方便的进行扩展；

策略模式的缺点：

1、策略相互独立，因此一些复杂的算法逻辑无法共享，造成一些资源浪费；

2、如果用户想采用什么策略，必须了解策略的实现，因此所有策略都需向外暴露，这是违背迪米特法则/最少知识原则的，也增加了用户对策略对象的使用成本。
<a href="//mp.weixin.qq.com/s?__biz=MzAxODE2MjM1MA==&mid=2651556974&idx=1&sn=2632dafaf94073989bb7fb5ab2610fc2&chksm=80255bafb752d2b93af37e76a2ee068c45e52a6b7d541d515d04807b18e41a8d697cbb0eac0f&mpshare=1&scene=1&srcid=&sharer_sharetime=1567996467029&sharer_shareid=7eef991d3e8f3a93c7682f2434cb1474&rd2werd=1#wechat_redirect" target="_blank" rel="noopener noreferrer">参考链接</a>
<h3 id="a3">代理模式</h3>

代理模式是为一个对象提供一个代用品或占位符，以便控制对它的访问

故事背景：

假设当 A 在心情好的时候收到花，小明表白成功的几率有 60%，而当 A 在心情差的时候收到花，小明表白的成功率无限趋近于 0。
小明跟 A 刚刚认识两天，还无法辨别 A 什么时候心情好。如果不合时宜地把花送给 A，花 被直接扔掉的可能性很大，这束花可是小明吃了 7 天泡面换来的。
但是 A 的朋友 B 却很了解 A，所以小明只管把花交给 B，B 会监听 A 的心情变化，然后选 择 A 心情好的时候把花转交给 A

代码实现:
``` javascript
const Flower = function(){};
const xiaoming = {
    sendFlower: function( target){
        const flower = new Flower();
        target.receiveFlower( flower ); 
    }
};
const B = {
    receiveFlower: function( flower ){
        A.listenGoodMood(function(){ 
            const flower = new Flower(); 
            A.receiveFlower( flower );
        }); 
    }
};
const A = {
    receiveFlower: function( flower ){
        // 监听 A 的好心情
        console.log( '收到花 ' + flower ); 
    },
    listenGoodMood: function( fn ){
        setTimeout(function(){ // 假设 10 秒之后 A 的心情变好
            fn(); 
        }, 10000 );
    } 
};
xiaoming.sendFlower( B );
```

由上面的例子可以引出两种代理模式

保护代理

代理 B 可以帮助 A 过滤掉一些请求，比如送花的人中年龄太大的或者没有宝马的，这种请求就可以直接在代理 B 处被拒绝掉

虚拟代理

假设现实中的花价格不菲，导致在程序世界里，new Flower 也是一个代价昂贵的操作， 那么我们可以把 new Flower 的操作交给代理 B 去执行，代理 B 会选择在 A 心情好时再执行 new Flower

虚拟代理实现图片预加载
``` javascript
const myImage = (function(){
    const imgNode = document.createElement( 'img' );     
    document.body.appendChild( imgNode );
    return {
        setSrc: function( src ){
             imgNode.src = src; 
        }
    } 
})();
const proxyImage = (function(){ 
    const img = new Image; 
    img.onload = function(){
        myImage.setSrc( this.src ); 
    }
    return {
        setSrc: function( src ){
             myImage.setSrc( 'file:// /C:/Users/svenzeng/Desktop/loading.gif' );
             img.src = src;  
        }
    } 
})();
proxyImage.setSrc('http://imgcache.qq.com/music/photo/k/000GGDys0yA0Nk.jpg' );
```

虚拟代理合并HTTP请求
假设我们在做一个文件同步的功能，当我们选中一个 checkbox 的时候，它对应的文件就会被同 步到另外一台备用服务器上面。当一次选中过多时，会产生频繁的网络请求。将带来很大的开销。可以通过一个代理函数 proxySynchronousFile 来收集一段时间之内的请求， 最后一次性发送给服务器 
``` javascript
const synchronousFile = function( id ){ 
    console.log( '开始同步文件，id 为: ' + id );
};
const proxySynchronousFile = (function(){
    const cache = [], // 保存一段时间内需要同步的 ID
    timer; // 定时器
    return function( id ){
        cache.push( id );
            if ( timer ){ // 保证不会覆盖已经启动的定时器
                 return; 
            }
        timer = setTimeout(function(){ 
            synchronousFile( cache.join( ',' ) ); 
            clearTimeout( timer ); // 清空定时器 
            timer = null;
            cache.length = 0; // 清空 ID 集合
        }, 2000 ); 
    }// 2 秒后向本体发送需要同步的 ID 集合
})();

const checkbox = document.getElementsByTagName( 'input' );
   for ( let i = 0, c; c = checkbox[ i++ ]; ){
      c.onclick = function(){
      if ( this.checked === true ){
          proxySynchronousFile( this.id ); }
      }
};
```
<a href="https://juejin.im/post/5c10ff28518825778a56cb3e" target="_blank" rel="noopener noreferrer">参考链接</a>
<h3 id="a4">观察者模式</h3>

定义对象间的一种一对多的依赖关系，当一个对象的状 态发生改变时，所有依赖于它的对象都将得到通知。在 JavaScript 开发中，我们一般用事件模型 来替代传统的发布—订阅模式。

DOM 事件

实际上，只要我们曾经在 DOM 节点上面绑定过事件函数，那我们就曾经使用过发布—订阅模式，来看看下面这两句简单的代码发生了什么事情:
``` javascript
document.body.addEventListener( 'click', function(){ alert(2);
}, false );
document.body.click(); // 模拟用户点击
```

发布-订阅模式的通用实现
``` javascript
const event = {
    clientList: [],
    listen: ( key, fn ) => {
        if ( !this.clientList[key] ){
            this.clientList[key] = []; 
        }
        this.clientList[key].push(fn) // 订阅的消息添加进缓存列表 
    },
    trigger: (...arg) => {
        const key = Array.prototype.shift.call( arg ),
        fns = this.clientList[key];

        if ( !fns || fns.length === 0 ){ // 如果没有绑定对应的消息 
            return false;
        }
        for( let i = 0, fn; fn = fns[i++]; ){
            fn.apply( this, arg ); // (2) // arg 是 trigger 时带上的参数
        } 
    },
    remove: ( key, fn ) => { 
        const fns = this.clientList[key];
        if ( !fns ){ // 如果 key 对应的消息没有被人订阅，则直接返回
            return false;
        }
        if ( !fn ){ // 如果没有传入具体的回调函数，表示需要取消 key 对应消息的所有订阅
            fns && (fns.length = 0);
        } else {
            for ( let l = fns.length - 1; l >=0; l-- ){ // 反向遍历订阅的回调函数列表 const _fn = fns[ l ];
                if ( _fn === fn ){
                    fns.splice( l, 1 );
                }
            }
        }
    }
};
再定义一个 installEvent 函数，这个函数可以给所有的对象都动态安装发布—订阅功能:
const installEvent = obj => { 
    for ( let i in event ){
        obj[ i ] = event[ i ]; 
    }
};
再来测试一番，我们给售楼处对象 salesOffices 动态增加发布—订阅功能: 
const salesOffices = {};
installEvent( salesOffices );

salesOffices.listen( 'squareMeter88', price => {
    console.log( '价格= ' + price );
});
salesOffices.listen( 'squareMeter100', price => {
    console.log( '价格= ' + price );
});

salesOffices.trigger( 'squareMeter88', 2000000 ); // 价格= 2000000
salesOffices.trigger( 'squareMeter100', 3000000 ); // 价格= 3000000
```
<h3 id="a5">命令模式</h3>

用于将一个请求封装成为对象，从而使你可用不同的请求对客户进行参数化；对请求排队或者记录请求日志，以及执行可撤销的操作。

菜单例子
``` javascript
// 顾客点餐
const Customer = function(command) {
    return {
        book: function(food, time) {
            return command.execute(food, time);
        },
        undo: function(menu) {
            command.undo(menu);
        }
    };
}

// 服务员，拥有点餐方法和撤销点餐方法
const foodCommand = function(cook){
    return {
        execute: function(food, time) {
            const timer = cook.willCook(food, time);
            return timer;
        }, 
        undo: function(food) {
            cook.unCook(food);
        }
    };
}

// 厨师
const cook = function() {
    return {
        willCook: function(food, time) {
            console.log('时间在' + time + "：开始煮：" + food);
            const timer = setTimeout(function() {
                console.log(food + '完成了');
            }, time);
            return timer;
        },
        unCook: function(timer) {
            clearTimeout(timer);
        }
    };
}

const command = foodCommand(cook());
const customer = Customer(command);
const receipt = customer.book('西红寺炒鸡蛋', 5000); // 5秒后炒完菜

customer.undo(receipt); //  做了取消操作，则不会炒菜
```
<h3> 代理模式与命令模式区别</h3>

 JavaScript 可以用高阶函数非常方便地实现命令模式。命令模式在 JavaScript 语言中是一种隐形的模式。

在代理(委托)模式中，调用者就是委托者，执行者就是被委托者，委托者和被委托者接口定义是相同的；在命令模式中，调用者不关注执行者的接口定义是否和它一致。

在调用时机上，代理模式的具体执行是只能在特定的调用者内部执行(接口相同)；命令模式的具体执行可以在任何调用者内部执行(接口不相同也可以)。
<h3 id="a6"> 迭代器模式</h3>

迭代器模式是指提供一种方法顺序访问一个聚合对象中的各个元素，而又不需要暴露该对象的内部表示。迭代器模式可以把迭代的过程从业务逻辑中分离出来,在使用迭代器模式之后，即使不关心对象的内部构造，也可以按顺序访问其中的每个元素。

内部迭代器
``` javascript
const each = function( ary, callback ){
    for ( const i = 0, l = ary.length; i < l; i++ ){
         callback.call( ary[i], i, ary[ i ] );
    }
};
each( [ 1, 2, 3 ], function( i, n ){ 
    alert ( [ i, n ] );
});
```

外部迭代器
``` javascript
const Iterator = function( obj ){ 
    const current = 0;
    const next = function(){ 
        current += 1;
    };
    const isDone = function(){
        return current >= obj.length;
    };
    const getCurrItem = function(){ 
        return obj[ current ];
    };
    return {
        next: next,
        isDone: isDone,
        getCurrItem: getCurrItem 
    };
}
const compare = function( iterator1, iterator2 ){
    while( !iterator1.isDone() && !iterator2.isDone() ){
    if ( iterator1.getCurrItem() !== iterator2.getCurrItem() ){ 
        throw new Error ( 'iterator1 和 iterator2 不相等' );
    } 
    iterator1.next(); 
    iterator2.next();
}
alert ( 'iterator1 和 iterator2 相等' ); }
const iterator1 = Iterator( [ 1, 2, 3 ] );
const iterator2 = Iterator( [ 1, 2, 3 ] );
compare( iterator1, iterator2 ); // 输出:iterator1 和 iterator2 相等
```

倒序迭代器
``` javascript
const reverseEach = function( ary, callback ){
    for ( let l = ary.length - 1; l >= 0; l-- ){
        callback( l, ary[ l ] ); }
    };
}
reverseEach( [ 0, 1, 2 ], function( i, n ){ 
    console.log( n ); // 分别输出:2, 1 ,0
});
```

中止迭代器
``` javascript
const each = function( ary, callback ){
    for ( let i = 0, l = ary.length; i < l; i++ ){
        if ( callback( i, ary[ i ] ) === false ){ 
            break;
        } 
    }
};
each( [ 1, 2, 3, 4, 5 ], function( i, n ){
    if ( n > 3 ){ 
        return false;
    }
    console.log( n );
});
```
<h3 id="a7">组合模式</h3>

组合模式将对象组合成树形结构，以表示“部分-整体”的层次结构。 除了用来表示树形结构之外，组合模式的另一个好处是通过对象的多态性表现，使得用户对单个对象和组合对象的使用具有一致性






``` javascript

```
### 未完，待续......

<h3 id="a8">8. 组合模式</h3>








``` javascript

```
<h3 id="a9">装饰器模式</h3>








``` javascript

```
<h3 id="a10"> 外观模式</h3>








``` javascript

```
<h3 id="a11"> 享元模式</h3>








``` javascript

```
<h3 id="a12"> 代理模式</h3>








``` javascript

```
<h3 id="a13"> 责任链模式</h3>








``` javascript

```
<h3 id="a14">命令模式</h3>








``` javascript

```
<h3 id="a15"> 解释器模式</h3>








``` javascript

```
<h3 id="a16"> 迭代器模式</h3>








``` javascript

```
<h3 id="a17">中介者模式</h3>








``` javascript

```
<h3 id="a18">备忘录模式</h3>








``` javascript

```
<h3 id="a19">观察者模式</h3>








``` javascript

```
<h3 id="a20">状态模式</h3>








``` javascript

```
<h3 id="a21">介绍</h3>








``` javascript

```
<h3 id="a22">验证</h3>








``` javascript

```
<h3 id="a23">验证</h3>








``` javascript

```