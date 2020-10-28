---
title: centos7安装防火墙firewalld
tags: 'cmd'
categories: 'Linux'
top_img: '../../img/linux.jpg'
---

默认情况下CentOS 7可能已经安装了firewalld服务，若确实没有安装直接使用yum命令安装即可：



## 先来安装
``` bash

# yum install -y firewalld

```
## 常用命令
安装完成后firewalld并没有运行，有必要先来熟悉下基本的命令：
``` bash
#查看运行状态
firewall-cmd --state
#启动firewall
systemctl start firewalld
#设置开机自启
systemctl enable firewalld
#删除开机自启
systemctl disable firewalld
#停止firewall
systemctl stop firewalld
```

注：elasticsearch需要开放9200,9300两个端口

``` bash

# firewall-cmd --add-port=9200/tcp --permanent  #永久开放9200端口
# firewall-cmd --add-port=9300/tcp --permanent  #永久开放9300端口
# firewall-cmd --reload  #重新加载
# firewall-cmd --list-all #查看防火墙配置
public (default)
  interfaces:
  sources:
  services: dhcpv6-client ssh
  ports: 9200/tcp 9300/tcp
  masquerade: no
  forward-ports:
  icmp-blocks:
  rich rules:
  ```


## 也可以直接修改配置文件
比如您将SSH端口修改为了2018，可尝试直接编辑firewalld的配置文件vi /etc/firewalld/zones/public.xml，里面加入一条规则：
``` bash
>port protocol="tcp" port="2018"/>
```

接着输入命令：systemctl start firewalld来启动firewalld，这样就不会阻挡在外啦。


## 一些常用的列子
放行某个指定的TCP端口，如放行80端口：
``` bash
firewall-cmd --zone=public --add-port=80/tcp --permanent
```

放行端口范围（8000-9000）：
``` bash
firewall-cmd --zone=public --add-port=8000-9000/tcp --permanent
```

查看已经放行的端口：
``` bash
firewall-cmd --zone=public --list-ports
```

删除某个已经放行的端口（6022）：
``` bash
firewall-cmd --zone=public --remove-port=6022/tcp --permanent
```

阻止某个IP（123.57.22.204）连接：
``` bash
firewall-cmd --permanent --add-rich-rule="rule family=ipv4 source address=123.57.22.204 reject"
```

注意上面的所有列子都需要输入： firewall-cmd --reload重载firewall使其生效。


