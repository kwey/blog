---
title: 使用Node 开发命令行程序
tags: 'CMD'
categories: 'Node'
top_img: '/img/node.jpg'
---

## 一、可执行脚本

1.demo文件头添加这一行

``` bash
#!/usr/bin/env node
console.log(hello world)
```
2. 在package.json中添加
``` javascript
"bin": {
    "hello": "./demo.js",
},
```
3.在命令行执行：（下篇文章介绍）
``` bash
npm  link
```
然后就可以执行 hello 命令 运行demo里面的内容。

## 二、yargs 模块

``` bash
npm i -save yargs
```

``` javascript

const argv = require('yargs')
.option('v', {
    alias : 'version',
    demand: true,     // 后面的参数是否可以省略
    default: '0.0.0',
    describe: '版本号',
    type: string,    
    boolean：true,  // 是否强制转换成布尔值
})
.option('b', {
    alias : 'branch',
})
.usage('Usage: kwe-cli [options]')  // 用法格式
.example('kwe-cli', 'init zero')       // 举个例子
.help('h')    // 显示帮助信息
.alias('h', 'help')
.argv;

console.log(hello，argv.v)
console.log(hello，argv._)
// 就可以获取-v /-b 后的参数：
hello -v wzw
// 打印  hello wzw(当没wzw的时候 会打印true)

// 也可以argv._来获取非连词线开头的参数
hello -v wzw w s y

// 打印：['w', 's', 'y']
```