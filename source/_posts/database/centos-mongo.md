---
title: CentOS 7上安装和配置MongoDB
tags: 'Mongodb'
categories: 'Databse'
top_img: '../../img/database.jpg'
---

[数据库以及 Node/Express 应用的数据库集成(Mongoose)](https://developer.mozilla.org/zh-CN/docs/learn/Server-side/Express_Nodejs/mongoose)

>MongoDB是提供高性能，高可用性和自动缩放的NoSQL数据库。 NoSQL数据库意味着，与MySQL或PostgreSQL不同，它不支持SQL（结构化查询语言）来检索或操纵存储的数据。 MongoDB不会将数据存储在表中，而是将数据存储在类似于JSON的“文档”结构中（在MongoDB中称为BSON）

## 在CentOS中添加MongoDB资源库
使用ssh root帐户连接到CentOS 7服务器：
```bash
ssh root@107.182.28.215 -p 29420
```

## 创建文件
```bash
cd /etc/yum.repos.d/

vi mongodb-org-4.0.repo 
```
填入如下内容：
```bash
[mongodb-org-4.0]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/4.0/x86_64/
gpgcheck=1
enabled=1
```
gpgkey=https://www.mongodb.org/static/pgp/server-4.0.asc

## 安装配置
```bash
sudo yum install -y mongodb-org
```
```bash
默认数据位置： /var/lib/mongo 

默认日志位置： /var/log/mongodb

配置文件位置：/etc/mongod.conf
```
## 开启服务
```bash
开启：systemctl start mongod

关闭：systemctl stop mongod

重启：systemctl restart mongod

开机自启：systemctl ennable mongod
```
（2）在用的：
```bash
创建目录：/data/db
        /data/logs
        
创建配置文件：/data/mongo.cfg
```
<a href="https://www.jianshu.com/p/f179ce608391" target="_blank">配置文件mongod.conf</a>、<a href="https://mib168.iteye.com/blog/1843186" target="_blank">csdn详解</a>
并填入：
```bash
systemLog:
# 日志为文件
  destination: file
# 文件位置
  path: /home/data/logs/k-blog.log
# 是否追加
  logAppend: true
#进程
processManagement:
# 守护进程方式
  fork: true
storage:
  dbPath: /home/data/db
net:
# 绑定IP，默认127.0.0.1，只能本机访问
  bindIp: 127.0.0.1
# 端口
  port: 27017
```

```bash
开启：mongod -f mongod.cfg 
关闭：mongo进入shell控制台，输入use admin，然后输入db.shutdownServer()关闭服务。
```

## 卸载软件
```bash
sudo yum erase $(rpm -qa | grep mongodb-org)
```
命令：
```bash
yum remove packageName
yum erase packageName
```
其中remove是只卸载软件，保留配置文件和数据文件，erase是卸载软件并删除其相关的文件。


