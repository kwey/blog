---
title: nuxt的运行原理
tags: 'nuxt'
categories: 'frame'
top_img: '/img/design.jpeg'
---
通过查看nuxt.js工程目录下的package.json文件，我们可以看到下列几条指令：
```javascript 
"scripts": {
    "dev": "nuxt",
    "build": "nuxt build",
    "start": "nuxt start",
    "generate": "nuxt generate"
}
```

结合官网的介绍，我们可以知道不同的指令对应着不同的功能：<table><thead><tr><th>指令</th><th>描述</th></tr></thead><tbody><tr><td>nuxt</td><td>开启一个监听3000端口的服务器，同时提供hot-reloading功能</td></tr><tr><td>nuxt build</td><td>构建整个应用，压缩合并JS和CSS文件（用于生产环境）</td></tr><tr><td>nuxt start</td><td>开启一个生产模式的服务器（必须先运行nuxt build命令）</td></tr><tr><td>nuxt generate</td><td>构建整个应用，并为每一个路由生成一个静态页面（用于静态服务器）</td></tr></tbody></table>
以上几条指令，也就是本文将要分析的重点：究竟这些指令的背后，nuxt都做了一些什么样的工作呢？
## 三、执行指令
打开nuxt.js的工程目录，进入到到bin目录，我们可以看到5个文件：
```
    |__ nuxt
    |__ nuxt-build
    |__ nuxt-dev
    |__ nuxt-generate
    |__ nuxt-start
```

每个文件对应着不同的指令。下面我们通过一张图来分析每一条指令的执行过程：
![](/img/nuxt1.png)

从上图可知，每一条指令基本都是做了这么几件事情：<li>
读取nuxt.config.js文件的配置；</li><li>
实例化Nuxt()类，并把上一步读取到的配置覆盖Nuxt()类的默认配置；</li><li>
执行各自具体的方法函数。</li>
对应代码如下（节选）：
```
var nuxtConfigFile = resolve(rootDir, 'nuxt.config.js')

var options = {}
if (fs.existsSync(nuxtConfigFile)) {
        options = require(nuxtConfigFile)
    }
if (typeof options.rootDir !== 'string') {
        options.rootDir = rootDir
    }

var nuxt = new Nuxt(options)
nuxt.build()
```

第一步读取配置以及配置的内容可以查看<a href="https://nuxtjs.org/api/configuration-build" rel="nofollow noreferrer" target="_blank">官网说明</a>，下面我们将会对第二步和第三步进行深入探讨。

## 四、Nuxt()类
进入到nuxt/lib目录，我们可以看到如下的文件目录结构：
```javascript
    |__ app
    |__ build
        |__ index.js
        |__ webpack
    |__ generate.js
    |__ nuxt.js
    |__ render.js
    |__ server.js
    |__ utils.js
    |__ views
    
```

目录当中的nuxt.js文件，就是我们要实例化的Nuxt()类的所在，让我们来看看它都包含一些什么内容，以及各自都有些什么作用：
![](/img/nuxt2.png)

上图中每一步都可以在具体的代码中自行浏览。在用户输入指令并实例化了Nuxt()类以后，实例化出来的nuxt对象就会执行图中打了绿色对勾的几个方法：build(), render(), renderRoute(), renderAndGetWindow()以及generate()方法。
同时，Nuxt()类也提供了一个close()公有方法，用于关闭其所开启的服务器。

## 五、build()方法
build()方法对应着nuxt/lib/build/index.js文件，其基本构成如下：
![](/img/nuxt3.png)

简单来说，build()方法在判断完运行条件后，会先初始化产出目录.nuxt，然后通过不同目录下的文件结构来生成一系列的配置，写入模板文件后输出到.nuxt目录。接下来，则会根据不同的开发环境来调用不同的webpack配置，运行不同的webpack构建方案。

## 六、render.js文件
在nuxt/lib目录下找到render.js文件，它包含着我们即将要分析的三个方法：render(), renderRoute(), renderAndGetWindow()。
![](/img/nuxt4.png)

通过这张图片，我们可以知道nuxt对于处理“客户端渲染”与“服务端渲染”的逻辑其实是非常清晰的。
首先，在render()方法在处理完一系列的路径问题后，会调用renderRoute()方法，获取响应所需内容并完成响应。
其中renderRoute()方法会判断当前响应是否应执行服务端渲染。如果是，则调用vue提供的bundleRenderer()方法，把html内容渲染完毕以后再整体输出；如果不是，则直接输出一个&lt;div id="__nuxt">&lt;/div>字符串，交由客户端渲染。
最后，通过renderAndGetWindow()来检查输出的html是否存在问题，然后发出通知，表明html可用。# ## 七、generate.js文件
最后我们来分析一下generate.js文件。我们知道nuxt generate指令会为page目录下的每一个页面文件单独生成一个html静态页面，功能非常贴心。那么generate.js到底是怎么工作的呢？
![](/img/nuxt5.png)

在执行nuxt generate时，它会先执行前文已经分析过的build()方法，产出编译后的文件；然后会初始化dist目录，调用resolveRouteParams()方法，读取产出后的路由配置并整理。然后通过fs.writeFile()等API，把内容挨个写入文件并输出，最后再统计总的generate()运行时间。