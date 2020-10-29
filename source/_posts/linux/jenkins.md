---
title: CentOS7下yum安装Jenkins
tags: 'centos'
categories: 'Linux'
top_img: '/img/linux.jpg'
---



## 准备条件：安装Java（<a href="https://www.webq.top/article/900" target="_blank" style="font-size: 14px;">CentOS下安装Java</a>1.8）
## 

## 1、Yum安装
yum源导入
``` bash
#添加Yum源
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo

#导入密钥
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
```

安装
``` bash
sudo yum install -y jenkins
```
## 2、开放端口
Jenkins站点的默认监听端口是8080(<a href="https://www.webq.top/article/899" target="_blank">防火墙安装看这</a>)
``` bash
sudo firewall-cmd --add-port=8080/tcp --permanent
sudo firewall-cmd --reload
```

## 3、安装完成后启动Jenkins：

* 检查Jenkins服务状态
sudo systemctl status jenkins
* 设置为开机自启动
sudo systemctl enable jenkins
* 启动Jenkins服务
sudo systemctl start jenkins
* 停止Jenkins服务
sudo service jenkins stop


（查看Jenkins开启成功「Active：active （running）」，就可以在浏览器里面查看：<a href="https://link.jianshu.com/?t=http://IP:Port" target="_blank" rel="nofollow">http://服务器IP:Jenkins启动的端口号Port</a>）## 4、相关配置
Jenkins配置文件地址：
``` bash
/etc/sysconfig/jenkins
```

这就是Jenkins的配置文件，可以在这里查看Jenkins默认的配置。
cat jenkins
这里介绍下三个比较重要的配置：

JENKINS_HOME

JENKINS_USER

JENKINS_PORT
JENKINS_HOME是Jenkins的主目录，Jenkins工作的目录都放在这里,Jenkins储存文件的地址,Jenkins的插件，生成的文件都在这个目录下。

``` bash
## Path:        Development/Jenkins
## Description: Jenkins Continuous Integration Server
## Type:        string
## Default:     "/var/lib/jenkins"
## ServiceRestart: jenkins
#
# Directory where Jenkins store its configuration and working
# files (checkouts, build reports, artifacts, ...).
#
JENKINS_HOME="/var/lib/jenkins"

```

JENKINS_USER 是Jenkins的用户，拥有$JENKINS_HOME和/var/log/jenkins的权限。
``` bash
## Type:        string
## Default:     "jenkins"
## ServiceRestart: jenkins
#
# Unix user account that runs the Jenkins daemon
# Be careful when you change this, as you need to update
# permissions of $JENKINS_HOME and /var/log/jenkins.
#
JENKINS_USER="jenkins"

```

JENKINS_PORT 是Jenkins的端口，默认端口是8080。

## Default:     8080
ServiceRestart: jenkins

Port Jenkins is listening on.

Set to -1 to disable

JENKINS_PORT="8080"
``` bash
## Type:        integer(0:65535)  
## Default:     8080
## ServiceRestart: jenkins
#
# Port Jenkins is listening on.
# Set to -1 to disable
#
JENKINS_PORT="8080"
```


