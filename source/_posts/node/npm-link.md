---
title: npm包开发调试
tags: 'npm'
categories: 'Node'
top_img: '../../img/node.jpg'
---
开发NPM模块的时候，有时我们会希望，边开发边试用，比如本地调试的时候， 
``` javascript
require('myModule')
```
会自动加载本机开发中的模块。Node规定，使用一个模块时，需要将其安装到全局的或项目的 node_modules 目录之中。对于开发中的模块，解决方法就是在全局的 node_modules 目录之中，生成一个符号链接，指向模块的本地目录。 
 
> npm link 就能起到这个作用，会自动建立这个符号链接。 

请设想这样一个场景，你开发了一个模块 myModule ，目录为 src/myModule ，你自己的项目 myProject 要用到这个模块，项目目录为 src/myProject 

## 第一步 
* 在模块目录（ src/myModule ）下运行 
```bash
# src/myModule$ npm link  
    npm link
```

会在NPM的全局模块目录内，生成一个符号链接文件，该文件的名字就是 
package.json 文件中指定的模块名
```bash
/path/to/global/node_modules/myModule -> src/myModule
```
这个时候，已经可以全局调用 myModule 模块了。但是，如果我们要让这个模块安装在项目内，还要进行下面的步骤。 
## 第二步
* 切换到项目目录，再次运行 
```bash
npm link 命令，并指定模块名。 
```
```bash
src/myProject$ npm link myModule
```
上面命令等同于生成了本地模块的符号链接。 
```bash
src/myProject/node_modules/myModule -> /path/to/global/node_modules/myModule
```  
然后，就可以在你的项目中，加载该模块了。 
``` javascript
var myModule = require('myModule');
```
这样一来， myModule 的任何变化，都可以直接反映在 myProject 项目之中。但是，这样也出现了风险，任何在 myProject 目录中对 myModule 的修改，都会反映到模块的源码中。 如果你的项目不再需要该模块，可以在项目目录内使用 
```bash
npm unlink 命令，删除符号链接。 

src/myProject$ npm unlink myModule
```