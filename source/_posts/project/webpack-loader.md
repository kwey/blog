---
title: webpack 编写一个loader
tags: 'github'
categories: 'project'
top_img: '../../img/git.png'
---

> loader就是一个node模块，它输出了一个函数。

当某种资源需要用这个loader转换时，这个函数会被调用。
并且，这个函数可以通过提供给它的this上下文访问Loader API。
reverse-txt-loader
定义
``` javascript
module.exports = function(src) {
      //src是原文件内容（abcde），下面对内容进行处理，这里是反转
  var result = src.split('').reverse().join(''); 
  //返回JavaScript源码，必须是String或者Buffer
  return `module.exports = '${result}'`;
}
```
// 使用
``` javascript
{
    test: /\\.txt$/,
    use: [
          {
              './path/reverse-txt-loader'
      }
    ]
}
```

## loader 工具库(Loader Utilities)
充分利用 <a href="https://github.com/webpack/loader-utils">loader-utils</a> 包。它提供了许多有用的工具，但最常用的一种工具是获取传递给 loader 的选项。<a href="https://github.com/webpack-contrib/schema-utils">schema-utils</a> 包配合 loader-utils，用于保证 loader 选项，进行与 JSON Schema 结构一致的校验。这里有一个简单使用两者的例子：

loader.js

``` javascript
import { getOptions } from 'loader-utils';
import validateOptions from 'schema-utils';

const schema = {
      type: 'object',
  properties: {
        test: {
          type: 'string'
    }
  }
};

export default function(source) {
      const options = getOptions(this);

  validateOptions(schema, options, 'Example Loader');

  // 对资源应用一些转换……

  return `export default ${ JSON.stringify(source) }`;
}
```

