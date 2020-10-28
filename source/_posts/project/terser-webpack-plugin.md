---
title: terser-webpack-plugin
tags: 'webpack'
categories: 'project'
top_img: '../../img/git.png'
---


<a href="https://github.com/webpack-contrib/terser-webpack-plugin" target="_blank">github</a>

## Getting Started
To begin, you'll need to install terser-webpack-plugin:
``` bash
$ npm install terser-webpack-plugin --save-dev
```

Then add the plugin to your webpack config. For example:

webpack.config.js
``` javascript
const TerserPlugin = require('terser-webpack-plugin');
module.exports = {
      optimization: {
        minimizer: [new TerserPlugin()],
  },
};
```

参数：

* test：
Type: String|RegExp|Array<String|RegExp> Default: /\\.m?js(\\?.*)?$/i


* include/exclude:

    Type: String|RegExp|Array<String|RegExp> Default: undefined


* chunkFilter:

    Type: Function<(chunk) -> boolean> Default: () => true

    允许过滤哪些块应压缩（默认情况下，所有块都压缩）。返回true使块压缩，否则返回false。
* cache:
    Type: 指定路径或者默认路径Boolean|String Default: false

    启用文件缓存。缓存目录的默认路径：node_modules/.cache/terser webpack plugin。

    _如果使用自己的minify功能，请正确阅读minify部分以了解缓存的无效性。


* cacheKeys:
    Type: Function<(defaultCacheKeys, file) -> Object> Default: defaultCacheKeys => defaultCacheKeys

* parallel:
    Type: Boolean|Number Default: false

    使用多进程并行运行来提高构建速度。默认并发运行数：os.cpus（）.length-1。
    _并行化可以显著加快构建速度，因此强烈推荐。

* sourceMap:
    Type: Boolean Default: false

    使用源映射将错误消息位置映射到模块（这会减慢编译速度）。

    如果使用自己的minify函数，请阅读minify部分以正确处理源映射。

    cheap-source-map  选项不适用于此插件。

* minify:
    Type: Function Default: undefined



``` javascript

module.exports = {
      optimization: {
        minimizer: [
            new TerserPlugin({
            test: /\\.js(\\?.*)?$/i,
            include: /\\/includes/,
            exclude: /\\/excludes/,
            chunkFilter: (chunk) => {
                // Exclude uglification for the `vendor` chunk
                if (chunk.name === 'vendor') {
                        return false;
                }

                return true;
            },
            cache: true / 'path/to/cache',
            cacheKeys: (defaultCacheKeys, file) => {
                defaultCacheKeys.myCacheKey = 'myCacheKeyValue';
 
                return defaultCacheKeys;
            },
            parallel: 4 / true,
            sourceMap: true,
            minify: (file, sourceMap) => {
                const extractedComments = []
                // Custom logic for extract comments
                const {error,map,code,warnings }=require('uglify-module') 
                // Or require('./path/to/uglify-module')
            .minify(file, {
                  /* Your options for minification */
            });
          return { error, map, code, warnings, extractedComments };
        },
      }),
    ],
  },
};
```

允许覆盖默认的缩小功能。默认插件使用terser包。用于使用和测试未发布的版本或分叉。
启用并行选项时，始终使用要求内部缩小功能。

## terserOptions:

Type: Object Default: default
<a href="https://github.com/terser/terser#minify-options" target="_blank">详细信息看这</a>

``` javascript
module.exports = {
      optimization: {
        minimizer: [
          new TerserPlugin({
            terserOptions: {
              ecma: undefined,
          warnings: false,
          parse: {},
          compress: {},
          mangle: true, // Note `mangle.properties` is `false` by default.
          module: false,
          output: null,
          toplevel: false,
          nameCache: null,
          ie8: false,
          keep_classnames: undefined,
          keep_fnames: false,
          safari10: false,
        },
      }),
    ],
  },
};
```

## extractComments:
Type: Boolean|String|RegExp|Function<(node, comment) -> Boolean|Object>|Object Default: false
是否将注释提取到单独的文件中（见详细信息）。

默认情况下，仅使用/^ \\**提取注释！|@保留@license@cc_on/i regexp条件并删除剩余注释。

如果原始文件名为foo.js，则注释将存储到foo.js.license。

terseroptions.output.comments选项指定是否保留注释，
即可以在提取其他注释的同时保留某些注释（例如注释），甚至可以保留已提取的注释。

condition：
Type: Boolean|String|RegExp|Function<(node, comment) -> Boolean|Object>

``` javascript
module.exports = {
      optimization: {
        minimizer: [
          new TerserPlugin({
                extractComments: {
                condition: /^\\**!|@preserve|@license|@cc_on/i,
                filename: 'extracted-comments.js',
                banner: (licenseFile) => {
                    return `License information can be found in ${licenseFile}`;
                 },
            },
        }),
    ],
  },
};

```
