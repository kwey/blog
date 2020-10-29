---
title: Linux定时任务 + mongodb自动备份
tags: 'cmd'
categories: 'Linux'
top_img: '/img/linux.jpg'
---

> mongodump -d k-blog -c articles -o ./

<a href="https://www.jianshu.com/p/0f1e009beb84" target="_blank">原文地址</a>

<a href="https://brickyang.github.io/2017/03/02/Linux-%E8%87%AA%E5%8A%A8%E5%A4%87%E4%BB%BD-MongoDB/" target="_blank">Linux 自动定时备份 MongoDB</a>

## mongodb自动整库备份脚本

``` bash
#!/bin/sh
DB_HOST="127.0.0.1"

DB_NAME="node_user"

OUT_DIR="/data/backup/mongod_bak/mongod_bak_now" #临时备份目录

TAR_DIR="/data/backup/mongod_bak/mongod_bak_list" #备份存放路径

DATE=$(date +%Y_%m_%d) #获取当前系统时间

echo "-----当前时间为$DATE-----"

DAYS=7 #DAYS=7代表删除7天前的备份，即只保留最近7天的备份

TAR_BAK="mongod_bak_$DATE.tar.gz" #最终保存的数据库备份文件名

cd $OUT_DIR

echo "-----删除原有备份文件-----"

find $OUT_DIR/ -print

rm -rf $OUT_DIR/*

mkdir -p $OUT_DIR/$DATE

echo "-----开始备份全部数据库-----"

mongodump -h $DB_HOST -o $OUT_DIR/$DATE #备份全部数据库，具体可以参照：  mongodump --help

echo "-----开始压缩备份文件-----"

tar -zcvf $TAR_DIR/$TAR_BAK $OUT_DIR/$DATE #压缩为.tar.gz格式

echo "-----删除7天前的备份文件-----"

find $TAR_DIR/ -mtime +$DAYS -delete #删除7天前的备份文件
```

## Mongodb整库还原 ----- mongorestore
``` bash
mongorestore -h dbhost -d dbname –directoryperdb dbdirectory
-h：MongoDB所在服务器地址
-d：需要恢复的数据库实例，例如：test，当然这个名称也可以和备份时候的不一样，比如test2
–directoryperdb：备份数据所在位置，例如：c:\\data\\dump\\test，这里为什么要多加一个test，而不是备份时候的dump，读者自己查看提示吧！
–-drop：恢复的时候，先删除当前数据，然后恢复备份的数据。就是说，备份后添加修改的数据都会被删除，慎用哦！
```

## 单个collection备份：
``` bash
mongoexport -h dbhost -d dbname -c collectionname -f collectionKey -o dbdirectory
-h: MongoDB所在服务器地址
-d: 需要恢复的数据库实例
-c: 需要恢复的集合
-f: 需要导出的字段(省略为所有字段)
-o: 表示导出的文件名
```

## 单个collection恢复：
``` bash
mongoimport -d dbhost -c collectionname –type csv –headerline –file
-type: 指明要导入的文件格式
-headerline: 批明不导入第一行，因为第一行是列名
-file: 指明要导入的文件路径
```

## Linux 定时任务   crond


1、默认crontab 的邮件位置为： /var/spool/mail/root

## crond简介：

1）、crond是一个linux下 的定时执行工具，可以在无需人工干预的情况下运行作业。
``` bash
　　/sbin/service crond start    //启动服务

　　/sbin/service crond stop     //关闭服务

　　/sbin/service crond restart  //重启服务

　　/sbin/service crond reload   //重新载入配置

　　/sbin/service crond status   //查看服务状态
```

2)、/etc/cron.d/ 这个目录用来存放任何要执行的crontab文件或脚本。


3）、脚本实例：
``` bash
##每天早上6点 
0 6 * * * echo "Good morning." &gt;&gt; /tmp/test.txt //注意单纯echo，从屏幕上看不到任何输出，因为cron把任何输出都email到root的信箱了。

##每两个小时 
0 */2 * * * echo "Have a break now." &gt;&gt; /tmp/test.txt  

##晚上11点到早上8点之间每两个小时和早上八点 
0 23-7/2，8 * * * echo "Have a good dream" &gt;&gt; /tmp/test.txt

##每个月的4号和每个礼拜的礼拜一到礼拜三的早上11点 
0 11 4 * 1-3 command line

##1月1日早上4点 
0 4 1 1 * command line SHELL=/bin/bash PATH=/sbin:/bin:/usr/sbin:/usr/bin MAILTO=root //如果出现错误，或者有数据输出，数据作为邮件发给这个帐号 HOME=/ 

##每小时执行/etc/cron.hourly内的脚本
01 * * * * root run-parts /etc/cron.hourly

##每天执行/etc/cron.daily内的脚本
02 4 * * * root run-parts /etc/cron.daily 

##每星期执行/etc/cron.weekly内的脚本
22 4 * * 0 root run-parts /etc/cron.weekly 

##每月去执行/etc/cron.monthly内的脚本 
42 4 1 * * root run-parts /etc/cron.monthly 

注意: "run-parts"这个参数了，如果去掉这个参数的话，后面就可以写要运行的某个脚本名，而不是文件夹名。 　 

##每天的下午4点、5点、6点的5 min、15 min、25 min、35 min、45 min、55 min时执行命令。 
5，15，25，35，45，55 16，17，18 * * * command

##每周一，三，五的下午3：00系统进入维护状态，重新启动系统。
00 15 * * 1，3，5 shutdown -r +5

##每小时的10分，40分执行用户目录下的innd/bbslin这个指令： 
10，40 * * * * innd/bbslink 

##每小时的1分执行用户目录下的bin/account这个指令： 
1 * * * * bin/account

##每天早晨三点二十分执行用户目录下如下所示的两个指令（每个指令以;分隔）： 
20 3 * * * （/bin/rm -f expire.ls logins.bad;bin/expire$#@62;expire.1st）　　

##每年的一月和四月，4号到9号的3点12分和3点55分执行/bin/rm -f expire.1st这个指令，并把结果添加在mm.txt这个文件之后（mm.txt文件位于用户自己的目录位置）。 
12,55 3 4-9 1,4 * /bin/rm -f expire.1st$#@62;$#@62;mm.txt 
```
### 对于时间的定义，一共分为6部分：
``` bash
      minute hour day-of-month month-of-year day-of-week commands 
合法值 00-59 00-23 01-31 01-12 0-6 (0 is sunday) 
```
``` bash
除了数字还有几个个特殊的符号就是  * 、/ 、- 、,  
```

"*" 代表所有的取值范围内的数字，

"/"代表每的意思,"/5"表示每5个单位，

"-"代表从某个数字到某个数字,

","分开几个离散的数字
``` bash
crontab -l   /  -r  /  -e
    -l 在标准输出上显示当前的crontab。 
　　-r 删除当前的crontab文件。 
　　-e 使用VISUAL或者EDITOR环境变量所指的编辑器编辑当前的crontab文件。当结束编辑离开时，编辑后的文件将自动安装。
```



