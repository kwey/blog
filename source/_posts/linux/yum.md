---
title: linux下安装软件--yum命令
tags: 'cmd'
categories: 'Linux'
top_img: '../../img/linux.jpg'
---

> 在安装yum的时候，yum会建立它的软件仓库，可以通过yum repolist来查看有哪些，yum库的定义目录为/etc/yum.repos.d,一般情况下，这些预装的库能满足我们的需要,如果自己添加repository需要相应URL和密钥。



## 1.准备：
centos7 修改yum源为阿里源,某下网络下速度比较快 首先是到yum源设置文件夹里
``` bash
   cd /etc/yum.repos.d
```
接着备份旧的配置文件
   sudo mv CentOS-Base.repo CentOS-Base.repo.bak
下载阿里源的文件,也可以是其他的源如（<a href="https://blog.csdn.net/inslow/article/details/54177191" target="_blank">修改CentOS默认yum源为国内yum镜像源</a>）
``` bash
   sudo wget -O CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
```
清理缓存
``` bash
    yum clean all
```
重新生成缓存
``` bash
    yum makecache 
```

## 2.列出已经安装的软件包
``` bash
yum list installed 列出所有已经安装的软件包
yum list packageName 在线查看软件包
yum list installed packageName 在已经安装的软件包中查看packageName软件包，可以通过此命令来查看是否安装此软件包
```
3.安装软件包
``` bash
yum install packageName 在线安装软件包

yum localinstall packageName 安装本地rpm软件包
```
4.升级软件包
``` bash
yum list updates 列出需要更新的软件包

yum update packageName 更新此软件包

yum update 更新所有列在yum list updates更新列表中的软件包
```
5.卸载软件
``` bash
yum remove packageName

yum erase packageName
```
其中remove是只卸载软件，保留配置文件和数据文件，erase是卸载软件并删除其相关的文件。

6.查看包依赖关系
``` bash
yum deplist packageName
```
7.查看系统中的某个文件是由哪个软件提供的
``` bash
yum provides  fileName
```