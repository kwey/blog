---
title: crontab 定时任务
tags: 'cmd'
categories: 'Linux'
top_img: '/img/linux.jpg'
---
>通过crontab 命令，我们可以在固定的间隔时间执行指定的系统指令或 shell script脚本。时间间隔的单位可以是分钟、小时、日、月、周及以上的任意组合


## <a href="https://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/crontab.html" target="_blank">crontab 定时任务</a>

<a href="http://www.cnblogs.com/peida/archive/2013/01/08/2850483.html" target="_blank">每天一个linux命令（50）：crontab命令</a>

<a href="https://segmentfault.com/a/1190000002628040" target="_blank">Linux 下执行定时任务 crontab 命令详解</a>

<a href="http://www.cnblogs.com/peida/tag/linux%E5%91%BD%E4%BB%A4/default.html?page=1" target="_blank">linux命令</a>

<a href="http://www.cnblogs.com/jianqingwang/p/6726589.html" target="_blank">Centos7:利用crontab定时执行任务</a>

## 19、crontab 定时任务

## 19.1. 命令格式<a href="https://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/crontab.html#id2" title="永久链接至标题"></a>
``` bash
crontab [-u user] file crontab [-u user] [ -e | -l | -r ]
```
## 19.2. 命令参数<a href="https://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/crontab.html#id3" title="永久链接至标题"></a>
* -u user：用来设定某个用户的crontab服务；
* file：file是命令文件的名字,表示将file做为crontab的任务列表文件并载入crontab。如果在命令行中没有指定这个文件，crontab命令将接受标准输入（键盘）上键入的命令，并将它们载入crontab。
* -e：编辑某个用户的crontab文件内容。如果不指定用户，则表示编辑当前用户的crontab文件。
* -l：显示某个用户的crontab文件内容，如果不指定用户，则表示显示当前用户的crontab文件内容。
* -r：从/var/spool/cron目录中删除某个用户的crontab文件，如果不指定用户，则默认删除当前用户的crontab文件。
* -i：在删除用户的crontab文件时给确认提示。

## 19.3. crontab的文件格式<a href="https://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/crontab.html#id4" title="永久链接至标题"></a>
分 时 日 月 星期 要运行的命令
第1列分钟0～59
第2列小时0～23（0表示子夜）
第3列日1～31
第4列月1～12
第5列星期0～7（0和7表示星期天）
第6列要运行的命令
## 19.4. 常用方法<a href="https://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/crontab.html#id5" title="永久链接至标题"></a>

## 创建一个新的crontab文件<a href="https://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/crontab.html#id6" title="永久链接至标题"></a>
向cron进程提交一个crontab文件之前，首先要设置环境变量EDITOR。cron进程根据它来确定使用哪个编辑器编辑crontab文件。9 9 %的UNIX和LINUX用户都使用vi，如果你也是这样，那么你就编辑$HOME目录下的. profile文件，在其中加入这样一行:

``` bash
EDITOR=vi; export EDITOR

```
然后保存并退出。不妨创建一个名为&lt;user> cron的文件，其中&lt;user>是用户名，例如， davecron。在该文件中加入如下的内容。

``` bash
# (put your own initials here)echo the date to the console every
# 15minutes between 6pm and 6am
0,15,30,45 18-06 * * * /bin/echo 'date' > /dev/console

```
保存并退出。注意前面5个域用空格分隔。
在上面的例子中，系统将每隔1 5分钟向控制台输出一次当前时间。如果系统崩溃或挂起，从最后所显示的时间就可以一眼看出系统是什么时间停止工作的。在有些系统中，用tty1来表示控制台，可以根据实际情况对上面的例子进行相应的修改。为了提交你刚刚创建的crontab文件，可以把这个新创建的文件作为cron命令的参数:

``` bash
$ crontab davecron

```
现在该文件已经提交给cron进程，它将每隔1 5分钟运行一次。同时，新创建文件的一个副本已经被放在/var/spool/cron目录中，文件名就是用户名(即dave)。
## 列出crontab文件<a href="https://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/crontab.html#id7" title="永久链接至标题"></a>
使用-l参数列出crontab文件:

``` bash
$ crontab -l
0,15,30,45 18-06 * * * /bin/echo `date` > dev/tty1

```
可以使用这种方法在$HOME目录中对crontab文件做一备份:

``` bash
$ crontab -l > $HOME/mycron

```
这样，一旦不小心误删了crontab文件，可以用上一节所讲述的方法迅速恢复。

## 编辑crontab文件<a href="https://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/crontab.html#id8" title="永久链接至标题"></a>
如果希望添加、删除或编辑crontab文件中的条目，而EDITOR环境变量又设置为vi，那么就可以用vi来编辑crontab文件:

``` bash
$ crontab -e

```
可以像使用vi编辑其他任何文件那样修改crontab文件并退出。如果修改了某些条目或添加了新的条目，那么在保存该文件时， cron会对其进行必要的完整性检查。如果其中的某个域出现了超出允许范围的值，它会提示你。 我们在编辑crontab文件时，没准会加入新的条目。例如，加入下面的一条：

``` bash
# DT:delete core files,at 3.30am on 1,7,14,21,26,26 days of each month
30 3 1,7,14,21,26 * * /bin/find -name 'core' -exec rm {} \\;

```
保存并退出。

注解
最好在crontab文件的每一个条目之上加入一条注释，这样就可以知道它的功能、运行时间，更为重要的是，知道这是哪位用户的定时作业。

## 删除crontab文件<a href="https://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/crontab.html#id9" title="永久链接至标题"></a>

``` bash
$crontab -r
```

## 19.5. 使用实例<a href="https://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/crontab.html#id10" title="永久链接至标题"></a>

## 实例1：每1分钟执行一次myCommand<a href="https://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/crontab.html#mycommand" title="永久链接至标题"></a>

``` bash
* * * * * myCommand
```

## 实例2：每小时的第3和第15分钟执行<a href="https://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/crontab.html#id11" title="永久链接至标题"></a>

``` bash
3,15 * * * * myCommand
```

##  实例3 在上午8点到11点的第3和第15分钟执行<a href="https://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/crontab.html#id12" title="永久链接至标题"></a>

``` bash
3,15 8-11 * * * myCommand

```
## 实例4：每隔两天的上午8点到11点的第3和第15分钟执行<a href="https://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/crontab.html#id13" title="永久链接至标题"></a>

``` bash
3,15 8-11 */2  *  * myCommand

```
## 实例5：每周一上午8点到11点的第3和第15分钟执行<a href="https://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/crontab.html#id14" title="永久链接至标题"></a>

``` bash
3,15 8-11 * * 1 myCommand

```

## 实例6：每晚的21:30重启smb<a href="https://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/crontab.html#smb" title="永久链接至标题"></a>

``` bash
30 21 * * * /etc/init.d/smb restart

```

## 实例7：每月1、10、22日的4 : 45重启smb<a href="https://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/crontab.html#id15" title="永久链接至标题"></a>

``` bash
45 4 1,10,22 * * /etc/init.d/smb restart

```

## 实例8：每周六、周日的1 : 10重启smb<a href="https://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/crontab.html#id16" title="永久链接至标题"></a>

``` bash
10 1 * * 6,0 /etc/init.d/smb restart

```

## 实例9：每天18 : 00至23 : 00之间每隔30分钟重启smb<a href="https://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/crontab.html#id17" title="永久链接至标题"></a>

``` bash
0,30 18-23 * * * /etc/init.d/smb restart

```

## 实例10：每星期六的晚上11 : 00 pm重启smb<a href="https://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/crontab.html#pmsmb" title="永久链接至标题"></a>

``` bash
0 23 * * 6 /etc/init.d/smb restart

```

## 实例11：每一小时重启smb<a href="https://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/crontab.html#id18" title="永久链接至标题"></a>

``` bash
* */1 * * * /etc/init.d/smb restart

```

## 实例12：晚上11点到早上7点之间，每隔一小时重启smb<a href="https://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/crontab.html#id19" title="永久链接至标题"></a>

``` bash
0 23-7 * * * /etc/init.d/smb restart

```

## 19.6. 使用注意事项<a href="https://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/crontab.html#id20" title="永久链接至标题"></a>


有时我们创建了一个crontab，但是这个任务却无法自动执行，而手动执行这个任务却没有问题，这种情况一般是由于在crontab文件中没有配置环境变量引起的。

在crontab文件中定义多个调度任务时，需要特别注环境变量的设置，因为我们手动执行某个任务时，是在当前shell环境下进行的，程序当然能找到环境变量，而系统自动执行任务调度时，是不会加载任何环境变量的，因此，就需要在crontab文件中指定任务运行所需的所有环境变量，这样，系统执行任务调度时就没有问题了。

不要假定cron知道所需要的特殊环境，它其实并不知道。所以你要保证在shelll脚本中提供所有必要的路径和环境变量，除了一些自动设置的全局变量。所以注意如下3点：

* 脚本中涉及文件路径时写全局路径；

* 脚本执行要用到java或其他环境变量时，通过source命令引入环境变量，如:

``` bash
cat start_cbp.sh
!/bin/sh
source /etc/profile
export RUN_CONF=/home/d139/conf/platform/cbp/cbp_jboss.conf
/usr/local/jboss-4.0.5/bin/run.sh -c mev &

```

当手动执行脚本OK，但是crontab死活不执行时,很可能是环境变量惹的祸，可尝试在crontab中直接引入环境变量解决问题。如:

``` bash
0 * * * * . /etc/profile;/bin/sh /var/www/java/audit_no_count/bin/restart_audit.sh

```

## 注意清理系统用户的邮件日志<a href="https://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/crontab.html#id22" title="永久链接至标题"></a>

每条任务调度执行完毕，系统都会将任务输出信息通过电子邮件的形式发送给当前系统用户，这样日积月累，日志信息会非常大，可能会影响系统的正常运行，因此，将每条任务进行重定向处理非常重要。 例如，可以在crontab文件中设置如下形式，忽略日志输出:

``` bash
0 */3 * * * /usr/local/apache2/apachectl restart >/dev/null 2>&1

```
“/dev/null 2>&1”表示先将标准输出重定向到/dev/null，然后将标准错误重定向到标准输出，由于标准输出已经重定向到了/dev/null，因此标准错误也会重定向到/dev/null，这样日志输出问题就解决了。

## 系统级任务调度与用户级任务调度<a href="https://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/crontab.html#id23" title="永久链接至标题"></a>

系统级任务调度主要完成系统的一些维护操作，用户级任务调度主要完成用户自定义的一些任务，可以将用户级任务调度放到系统级任务调度来完成（不建议这么做），但是反过来却不行，root用户的任务调度操作可以通过”crontab –uroot –e”来设置，也可以将调度任务直接写入/etc/crontab文件，需要注意的是，如果要定义一个定时重启系统的任务，就必须将任务放到/etc/crontab文件，即使在root用户下创建一个定时重启系统的任务也是无效的。

## 其他注意事项<a href="https://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/crontab.html#id24" title="永久链接至标题"></a>

新创建的cron job，不会马上执行，至少要过2分钟才执行。如果重启cron则马上执行。

当crontab失效时，可以尝试/etc/init.d/crond restart解决问题。或者查看日志看某个job有没有执行/报错tail -f /var/log/cron。

千万别乱运行crontab -r。它从Crontab目录（/var/spool/cron）中删除用户的Crontab文件。删除了该用户的所有crontab都没了。

在crontab中%是有特殊含义的，表示换行的意思。如果要用的话必须进行转义%，如经常用的date ‘+%Y%m%d’在crontab里是不会执行的，应该换成date ‘+%Y%m%d’。

更新系统时间时区后需要重启cron,在ubuntu中服务名为cron:

``` bash
$service cron restart

```
ubuntu下启动、停止与重启cron:

``` bash
$sudo /etc/init.d/cron start
$sudo /etc/init.d/cron stop
$sudo /etc/init.d/cron restart
```
