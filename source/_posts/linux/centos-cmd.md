---
title: centos7 常用命令【systemctl替换service】
tags: 'centos'
categories: 'Linux'
top_img: '/img/linux.jpg'
---


centos7 上面启动服务以及关闭服务已经不是以前的service stop/start xxxx了

而是systemctl命令，不过用service他会有一个提醒你用systemctl",
* "content": ' ' 看所有网卡IP地址——ip addr
* 启动防火墙——systemctl start firewalld.service
* 停止防火墙——systemctl stop firewalld.service
* 查看firewalld防火墙状态——firewall-cmd --state
* 禁止防火墙开机启动——systemctl disable firewalld.service
* 列出正在运行的服务状态——systemctl
* 启动一个服务—— systemctl start postfix.service
* 关闭一个服务—— systemctl stop postfix.servic
* 重启一个服务：—— systemctl restart postfix.service
* 显示一个服务的状态—— systemctl status postfix.service
* 在开机时启用一个服务—— systemctl enable postfix.service
* 在开机时禁用一个服务—— systemctl disable postfix.service
* 查看服务是否开机启动—— systemctl is-enabled postfix.service;echo $?
* 查看已启动的服务列表—— systemctl list-unit-files|grep enabled
* 设置系统默认启动运行级别3—— ln -sf /lib/systemd/system/multi-user.target /etc/systemd/system/default.target
* 设置系统默认启动运行级别5—— ln -sf/lib/systemd/system/graphical.target/etc/systemd/system/default.target