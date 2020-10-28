---
title: npm 包发布流程
tags: 'npm'
categories: 'Node'
top_img: '../../img/node.jpg'
---

## [【npm】利用npm安装/删除/发布/更新/撤销发布包](https://www.cnblogs.com/penghuwan/p/6973702.html)

* npm adduser用于在[npmjs.com](http://npmjs.com)注册一个用户。
*  已注册  npm login
*  npm publish

>注意：登录时报错：e409 Conflict
```bash
Registry returned 409 for PUT on http://registry.npm.taobao.org/-/user/org.couchdb.user:666: conflict
```

出现这个错误最大的可能性是我们把registry选项替换成了淘宝的镜像


登录前确保npm源为官方源：
```bash
nrm  ls
nrm use npm
```

```bash
npm config set registry https://registry.npmjs.org/
```

### [解决方案](http://zccbbg.top/2018/12/24/npm-adduser-%E6%8A%A5%E9%94%99e409-Conflict/#%E8%A7%A3%E5%86%B3%E6%96%B9%E6%A1%88)
```bash
npm login --registry http://registry.npmjs.org

npm publish --registry http://registry.npmjs.org
```

细节：
* 发布一个beta版，默认为latest
```bash
npm publish --tag beta
```

* 如果想废弃某个版本的模块，可以使用npm deprecate命令。
```bash
$ npm deprecate my-thing@"<  0.2.3" "critical bug fixed in v0.2.3"

# 运行上面的命令以后，小于0.2.3版本的模块的package.json都会写入一行警告，用户安装这些版本时，这行警告就会在命令行显示
```

* 模块的维护者可以发布新版本。npm owner命令用于管理模块的维护者。
```bash
# 列出指定模块的维护者
$ npm owner ls < package name >

# 新增维护者
$ npm owner add < user > < package name >

# 删除维护者
$ npm owner rm < user > < package name >
```