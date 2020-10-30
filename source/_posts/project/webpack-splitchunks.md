---
title: webpack 4 Code Splitting 的 splitChunks
tags: 'webpack'
categories: 'project'
top_img: '/img/git.png'
---
> webpack 4 废弃了之前的不怎么好用的 CommonsChunk，取而代之的是 SplitChunks

## Code Splitting
首先 webpack 总共提供了<span style="font-size: large; color: rgb(249, 150, 59);">三种办法来实现 Code Splitting，如下：<ul><li>入口配置：entry 入口使用多个入口文件；</li><li>抽取公有代码：使用 SplitChunks 抽取公有代码；</li><li>动态加载 ：动态加载一些代码。</li></ul>
## SplitChunks
首先我们所说的 SplitChunks 是由 webpack 4 内置的 <a href="https://webpack.js.org/plugins/split-chunks-plugin/" target="_blank">SplitChunksPlugin</a> 插件提供的能力，可直接在 optimization 选项中配置，其默认配置如下：

``` javascript

module.exports = {
    //...
    optimization: {
        splitChunks: {
            chunks: 'async', 
            minSize: 30000,
            maxSize: 0,
            minChunks: 1,
            maxAsyncRequests: 5,
            maxInitialRequests: 3,
            automaticNameDelimiter: '~',
            name: true,
            cacheGroups: {
                vendors: {
                    test: /[\\\\/]node_modules[\\\\/]/,
                    priority: -10
                },
                default: {
                    minChunks: 2,
                    priority: -20,
                    reuseExistingChunk: true
                }
            }
        }
    }
};

```

参数说明如下：<ul><li>chunks：表示从哪些chunks里面抽取代码，除了三个可选字符串值 initial、async、all 之外，还可以通过函数来过滤所需的 chunks；</li><li>minSize：表示抽取出来的文件在压缩前的最小大小，默认为 30000；</li><li>maxSize：表示抽取出来的文件在压缩前的最大大小，默认为 0，表示不限制最大大小；</li><li>minChunks：表示被引用次数，默认为1；</li><li>maxAsyncRequests：最大的按需(异步)加载次数，默认为 5；</li><li>maxInitialRequests：最大的初始化加载次数，默认为 3；</li><li>automaticNameDelimiter：抽取出来的文件的自动生成名字的分割符，默认为 ~；</li><li>name：抽取出来文件的名字，默认为 true，表示自动生成文件名；</li><li>cacheGroups: 缓存组。（这才是配置的关键）</li></ul><h3 id="cachegroups">cacheGroups</h3>
上面的那么多参数，其实都可以不用管，cacheGroups 才是我们配置的关键。它可以继承/覆盖上面 splitChunks 中所有的参数值，除此之外还额外提供了三个配置，分别为：test, priority 和 reuseExistingChunk。<ul><li>test: 表示要过滤 modules，默认为所有的 modules，可匹配模块路径或 chunk 名字，当匹配的是 chunk 名字的时候，其里面的所有 modules 都会选中；</li><li>priority：表示抽取权重，数字越大表示优先级越高。因为一个 module 可能会满足多个 cacheGroups 的条件，那么抽取到哪个就由权重最高的说了算；</li><li>reuseExistingChunk：表示是否使用已有的 chunk，如果为 true 则表示如果当前的 chunk 包含的模块已经被抽取出去了，那么将不会重新生成新的。</li></ul>
<h3 id="-">配置实战</h3>
一般来说我们常用的配置都是 common + page 的形式。而 page 在 entry 入口的时候就已经配置好了。那么现在就只剩下 common 的处理，这里讨论几种方案：

## 一刀切

下面我们把所有 node_modules 的模块被不同的 chunk 引入超过 1 次的抽取为 common。



``` javascript

cacheGroups: {
        common: {
        test: /[\\\\/]node_modules[\\\\/]/,
        name: 'common',
        chunks: 'initial',
        priority: 2,
        minChunks: 2,
        },
}
或者干脆把所有模块被不同的 chunk 引入超过 1 次的抽取为 common。

cacheGroups: {
    common: {
        name: 'common',
        chunks: 'initial',
        priority: 2,
        minChunks: 2,
    },
}

```

### 进一步抽取


``` javascript

cacheGroups: {
        reactBase: {
        name: 'reactBase',
        test: (module) => {
            return /react|redux|prop-types/.test(module.context);
        },
        chunks: 'initial',
        priority: 10,
        },
        common: {
            name: 'common',
            chunks: 'initial',
            priority: 2,
            minChunks: 2,
        },
}
```



## CSS 配置
同样对于通过 <a href="https://webpack.js.org/plugins/mini-css-extract-plugin/" target="_blank">MiniCssExtractPlugin</a> 生成的 CSS 文件也可以通过 SplitChunks 来进行抽取公有样式等。
如下表示将所有 CSS 文件打包为一个（注意将权重设置为最高，不然可能其他的 cacheGroups 会提前打包一部分样式文件）：



``` javascript

module.exports = {
        optimization: {
        splitChunks: {
            cacheGroups: {
                styles: {
                    name: 'styles',
                    test: /\\.css$/,
                    chunks: 'all',
                    enforce: true,
                    priority: 20, 
                }
            }
        }
    }
}
```