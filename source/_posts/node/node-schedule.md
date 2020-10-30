---
title: Nodejs定时任务（node-schedule)
tags: 'schedule'
categories: 'Node'
top_img: '/img/node.jpg'
---
> 定时导出某些数据、定时发送消息或邮件给用户、定时备份什么类型的文件等等


### 安装
```bash       
npm install node-schedule --save 或者 yarn add node-schedule
```
### 用法

## 1、Cron风格定时器

``` javascript
const schedule = require('node-schedule');

const  scheduleCronstyle = ()=>{
    //每分钟的第30秒定时执行一次:
    schedule.scheduleJob('30 * * * * *',()=>{
        console.log('scheduleCronstyle:' + new Date());
    }); 
}

scheduleCronstyle();

```


schedule.scheduleJob的回调函数中写入要执行的任务代码，一个定时器就完成了！
## 规则参数讲解    *代表通配符


``` javascript
*  *  *  *  *  *
┬ ┬ ┬ ┬ ┬ ┬
│ │ │ │ │  |
│ │ │ │ │ └ day of week (0 - 7) (0 or 7 is Sun)
│ │ │ │ └───── month (1 - 12)
│ │ │ └────────── day of month (1 - 31)
│ │ └─────────────── hour (0 - 23)
│ └──────────────────── minute (0 - 59)
└───────────────────────── second (0 - 59, OPTIONAL)

```


6个占位符从左到右分别代表：秒、分、时、日、月、周几

*表示通配符，匹配任意，当秒是*时，表示任意秒数都触发，其它类推

下面可以看看以下传入参数分别代表的意思

``` javascript
每分钟的第30秒触发： '30 * * * * *'

每小时的1分30秒触发 ：'30 1 * * * *'

每天的凌晨1点1分30秒触发 ：'30 1 1 * * *'

每月的1日1点1分30秒触发 ：'30 1 1 1 * *'

2016年的1月1日1点1分30秒触发 ：'30 1 1 1 2016 *'

每周1的1点1分30秒触发 ：'30 1 1 * * 1'

```


每个参数还可以传入数值范围:

``` javascript
const task1 = ()=>{
    //每分钟的1-10秒都会触发，其它通配符依次类推
    schedule.scheduleJob('1-10 * * * * *', ()=>{
    console.log('scheduleCronstyle:'+ new Date());
    })
}

task1()

```


每五分钟执行一个cron任务 = */5 * * * *不支持的cron特性
一般的, W (最近的工作日), L (一个月/星期的最后一天), 以及 # (月的第n个星期) 是不支持的. 大多数流行的cron特性应该都能工作。
<a href="https://github.com/harrisiirak/cron-parser" target="_blank">cron-parser 用来解析crontab指令</a>## 2、基于日期的调度
就是说你特别想要一个函数在 2012年12月12日早上5:30执行。 记住在JavaScript中- 0 - 星期一, 11 - 十二月.（意思就是星期数和月份数都是从0开始计数的）

``` javascript
var schedule = require('node-schedule');
var date = new Date(2012, 11, 21, 5, 30, 0);

var j = schedule.scheduleJob(date, function(){
        console.log('世界将在今天走向 结束.');
    });
```
    
要在未来使用当前数据，你可以使用绑定:

``` javascript
var schedule = require('node-schedule');
var date = new Date(2012, 11, 21, 5, 30, 0);
var x = 'Tada!';
var j = schedule.scheduleJob(date, function(y){
    console.log(y);
}.bind(null,x));
x = 'Changing Data';
```

当调度的任务运行时，这个将会打印出'Tada!'，而不是 'Changing Data'， 这个x会在调度后立即更改.
## 3、递归循环规则调度

你可以创建递归规则来指定任务在何时重新调用。举个例子，考虑这个规则，将在每个小时的第42分钟执行函数:

``` javascript
var schedule = require('node-schedule');

var rule = new schedule.RecurrenceRule();
rule.minute = 42;

var j = schedule.scheduleJob(rule, function(){
        console.log('生命，宇宙，一切的答案。。。!');
    });

```


你也可以使用数组来指定一个允许值的列表,Range

对象来指定一个系列的开始值和结束值，带有可选的步骤参数。举个例子，这个将在星期4，星期5，星期6和星期天的下午五点答应一个信息：

``` javascript
var rule = new schedule.RecurrenceRule();
rule.dayOfWeek = [0, new schedule.Range(4, 6)];
rule.hour = 17;
rule.minute = 0;

var j = schedule.scheduleJob(rule, function(){
    console.log('今天我碰到klren了!');
});

```

### 递归规则的属性
<ul>
<li>second</li>
<li>minute</li>
<li>hour</li>
<li>date</li>
<li>month</li>
<li>year</li>
<li>dayOfWeek</li>
</ul>


注意: 值得注意的时递归规则的默认的第一个属性是null (除了第二个,对于熟悉cron，知道默认为0). 如果我们之前没有明确地设定minute为0, 信息将会在下面时间打印 5:00pm, 5:01pm, 5:02pm, ..., 5:59pm. 或许这不是你想要的.

### 对象字面化语法

让事情变得简单一点，一个对象字面化语法也是支持的，就像这个例子，将会在每个星期天的下午两点半打印信息：

``` javascript
var j = schedule.scheduleJob({hour: 14, minute: 30, dayOfWeek: 0}, function(){
        console.log('到了喝茶的时间!');
    });

```

### 设置开始时间和结束时间

这个例子中，它将在五秒后开始，然后十秒后结束.和之前一样支持规则。

``` javascript
let startTime = new Date(Date.now() + 5000);
let endTime = new Date(startTime.getTime() + 5000);
var j = schedule.scheduleJob({ start: startTime, end: endTime, rule: '*/1 * * * * *' }, function(){
        console.log('到了喝茶时间!');
    });

```

## 处理任务和任务调度

这儿有一些函数来从一个任务中获取信息以及处理任务和调度
### job.cancel(reshedule)

你可以让任何任务失效，使用 cancel() 方法:

``` javascript
j.cancel();

```


所有的计划调用将会被取消。当你设置 reschedule 参数为true，然后任务将在之后重新排列。
### job.cancelNext(reshedule)

这个方法将取消下一个计划的调度或者任务.

当你设置 reschedule 参数为true，然后任务将在之后重新排列。
### job.reschedule(spec)

这个方法将取消所有挂起的调度，然后使用给定的规则重新注册任务.

将返回 true/false 来说明成功/失败.
### job.nextInvocation()

这个方法返回一个日期对象为这个任务的下一次调用计划，如果没有调度安排，则返回null.