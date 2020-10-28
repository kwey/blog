---
title: centos7 常用命令【systemctl替换service】
tags: 'cmd'
categories: 'Linux'
top_img: '../../img/linux.jpg'
---
> service crond status



1、先手动执行定时任务以此来判断脚本是否有问题。

2、确认服务器是否开启定时任务计划服务

命令：
```bash
service crond status

service crond start
```

3、查看crontab执行记录

如果出现了crontab定时任务不执行的情况，首先需要定位问题，那么就需要通过日志来确定问题所在。

crontab的日志位置一般位于/var/log/cron，利用下面的语句即可查看日志。
```bash
tail -f /var/log/cron
```
上面的/var/log/cron只会记录是否执行了某些计划的脚本，但是具体执行是否正确以及脚本执行过程中的一些信息linux

会通过邮件形式发送到给该用户。对于root用户该邮件记录位于/var/spool/mail/root，通过以下命令可以查看最近的crontab执行情况。
```bash
tail -f /var/spool/mail/root
```
mail邮件一般只会记录脚本执行成功与否，如果执行失败，无法给出进一步的错误信息，这时需要我们将语句执行的错误信息

重定向至文件中，这样可以很方便的查看错误信息
