---
title: webpack 编写一个plugin
tags: 'webpack'
categories: 'project'
top_img: '../../img/git.png'
---

> 创建插件比创建 loader 更加高级，因为你将需要理解一些 webpack 底层的内部特性来实现相应的钩子，所以做好阅读一些源码的准备！

<a href="https://webpack.docschina.org/contribute/writing-a-plugin/" target="_blank">编写一个插件</a>

一个插件由以下构成
* 一个具名 JavaScript 函数。
* 在它的原型上定义 apply 方法。
* 指定一个触及到 webpack 本身的 <a href="https://webpack.docschina.org/api/compiler-hooks/">事件钩子</a>。
* 操作 webpack 内部的实例特定数据。
* 在实现功能后调用 webpack 提供的 callback。



``` javascript
// 一个 JavaScript class
class MyExampleWebpackPlugin {
      // 将 `apply` 定义为其原型方法，此方法以 compiler 作为参数
  apply(compiler) {
        // 指定要附加到的事件钩子函数
    compiler.hooks.emit.tapAsync(
          'MyExampleWebpackPlugin',
      (compilation, callback) => {
            console.log('This is an example plugin!');
        console.log('Here’s the `compilation`
        object which represents a single build of assets:', compilation);

        // 使用 webpack 提供的 plugin API 操作构建结果
        compilation.addModule(/* ... */);

        callback();
      }
    );
  }
}
```

插件是由一个构造函数（此构造函数上的 prototype 对象具有 apply 方法）的所实例化出来的。这个 apply方法在安装插件时，会被 webpack compiler 调用一次。apply 方法可以接收一个 webpack compiler 对象的引用，从而可以在回调函数中访问到 compiler 对象。




然后，要使用这个插件，在你的 webpack 配置的 plugins 数组中添加一个实例：
``` javascript
// webpack.config.js
var HelloWorldPlugin = require('hello-world');

module.exports = {
      // ... 这里是其他配置 ...
  plugins: [new HelloWorldPlugin({ options: true })]
};
```

## compiler 和 compilation

在插件开发中最重要的两个资源就是 compiler 和 compilation 对象。理解它们的角色是扩展 webpack 引擎重要的第一步。
``` javascript
class HelloCompilationPlugin {
      apply(compiler) {
        // tap(触及) 到 compilation hook，而在 callback 回调时，会将 compilation 对象作为参数，
    compiler.hooks.compilation.tap('HelloCompilationPlugin', compilation => {
          // 现在，通过 compilation 对象，我们可以 tap(触及) 到各种可用的 hooks 了
      compilation.hooks.optimize.tap('HelloCompilationPlugin', () => {
            console.log('正在优化资源。');
      });
    });
  }
}

module.exports = HelloCompilationPlugin;
```










