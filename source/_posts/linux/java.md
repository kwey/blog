---
title: CentOS7下yum安装Java
tags: 'java'
categories: 'Linux'
top_img: '/img/linux.jpg'
---

## 一：查看yum包含的jdk版本

``` bash
yum search java 或者 yum list java*
```


<table><thead><tr><th>版本</th><th align="center">jre</th><th align="right">jdk</th></tr></thead><tbody><tr><td>1.8</td><td align="center">java-1.8.0-openjdk.x86_64</td><td align="right">java-1.8.0-openjdk-devel.x86_64</td></tr><tr><td>1.7</td><td align="center">java-1.7.0-openjdk.x86_64</td><td align="right">java-1.7.0-openjdk-devel.x86_64</td></tr><tr><td>1.6</td><td align="center">java-1.6.0-openjdk.x86_64</td><td align="right">java-1.6.0-openjdk-devel.x86_64</td></tr></tbody></table>

## 二、安装jdk
此次选择java-1.8.0-openjdk-devel.x86_64 : OpenJDK Development Environment
``` bash
yum install java-1.8.0-openjdk-devel.x86_64
```

## 


## 注：也可以安装1.8.0的所有文件
```bash
yum install java-1.8.0-openjdk* -y
```

## 三： 使用命令检查是否安装成功
```bash
java -version
```