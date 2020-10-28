---
title: Centos7 安装nvm、node
tags: 'centos'
categories: 'Linux'
top_img: '../../img/linux.jpg'
---

首先：
安装nvm
## 下载命令

```bash
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh | bash
```
或者

```bash
wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh | bash
```

## 下载完成后加入系统环境

```bash
source   ~/.bashrc
```

查看 NVM 版本list

```bash
nvm list-romote
```

 

4、安装需要的node版本
```bash
nvm install  v11.9.0
```
查看当前机器已安装版本号

```bash
nvm list
```
切换node版本

```bash
nvm use v11.9.0
```
7、设置默认的node版本




```bash
nvm alias default v11.9.0
```

n是Node的一个模块，作者是<a href="https://github.com/visionmedia" style="font-family: &quot;Source Sans Pro&quot;, &quot;Helvetica Neue&quot;, Arial, sans-serif;">TJ Holowaychuk</a>（<a href="http://expressjs.com/" style="font-family: &quot;Source Sans Pro&quot;, &quot;Helvetica Neue&quot;, Arial, sans-serif;">Express</a>框架作者）


n与nvm的区别
* n是 npm 的一个全局模块，安装n之前需要先安装node。 
nvm是一个独立软件包。

* 在安装的时候，n会先将指定版本的 node 存储下来，然后将其复制到我们熟知的路径/usr/local/bin，非常简单明了。当然由于n会操作到非用户目录，所以需要加sudo来执行命令。

    在安装的时候，nvm将不同的 node 版本存储到~/.nvm/&lt;version&gt;/下，然后修改$PATH，将指定版本的 node 路径加入，这样我们调用的 node 命令即是使用指定版本的 node。

所以，对于 node 路径，n永远是/usr/local/bin；nvm需要手动指定路径。

n对全局模块毫无作为，因此有可能在切换了 node 版本后发生全局模块执行出错的问题；nvm的全局模块存在于各自版本的沙箱中，切换版本后需要重新安装，不同版本间也不存在任何冲突。

>从上面两点可以看出，nvm的使用比n要复杂一些。但是从安全角度，还是用nvm
