---
title: SSR开启Google的BBR内核脚本加速TCP
tags: 'centos'
categories: 'Linux'
top_img: '../../img/linux.jpg'
---

> Google 开源了其 TCP BBR 拥塞控制算法，并提交到了 Linux 内核，从 4.9 开始，Linux 内核已经用上了该算法。根据以往的传统，Google 总是先在自家的生产环境上线运用后，才会将代码开源，此次也不例外。


BBR 这个特性其实是在 Linux 内核 4.9 才计划加入的。所以，要开启BBR，需要内核版本在Linux kernel 4.9以上，根据实地测试，在部署了最新版内核并开启了 TCP BBR 的机器上，网速甚至可以提升好几个数量级。下面纪录一下如何升级Linux内核，并且开启BBR：

下载bbr 安装脚本：

``` bash
wget --no-check-certificate https://github.com/teddysun/across/raw/master/bbr.sh && chmod +x bbr.sh && ./bbr.sh
```

安装成功后重启服务器

``` bash
shutdown -r now
```

查看内核版本，如果返回值含有4.13或以上版本, 就表示安装成功了。

``` bash
uname -r
```

``` bash
sysctl net.ipv4.tcp_available_congestion_control 
```

 返回：net.ipv4.tcp_available_congestion_control = bbr cubic reno
``` bash

sysctl net.ipv4.tcp_congestion_control
```

返回：net.ipv4.tcp_congestion_control = bbr
``` bash

sysctl net.core.default_qdisc
```

返回：net.core.default_qdisc = fq
``` bash

lsmod | grep bbr
```

返回：tcp_bbr 模块即说明 bbr 已启动。注意：并不是所有的 VPS 都会有此返回值，若没有也属正常。


<h3>内核升级方法： </h3>
如果是 CentOS 系统，执行如下命令即可升级内核： 
 yum --enablerepo=elrepo-kernel -y install kernel-ml kernel-ml-devel 


CentOS 6 的话，执行命令： 
 sed -i 's/^default=.*/default=0/g' /boot/grub/grub.conf 


CentOS 7 的话，执行命令：
 grub2-set-default 0 


如果是 Debian/Ubuntu 系统，则需要手动下载最新版内核来安装升级。 
去<a href="https://kernel.ubuntu.com/~kernel-ppa/mainline/" target="_blank">这里下载</a>最新版的内核 deb 安装包。

如果系统是 64 位，则下载 amd64 的 linux-image 中含有 generic 这个 deb 包； 

如果系统是 32 位，则下载 i386 的 linux-image 中含有 generic 这个 deb 包； 

安装的命令如下（以最新版的 64 位 4.12.4 举例而已，请替换为下载好的 deb 包）：

```bash
 dpkg -i linux-image-4.12.4-041204-generic_4.12.4-041204.201707271932_amd64.deb 
```

安装完成后，再执行命令：
```bash
 /usr/sbin/update-grub 
```

最后，重启 VPS 即可。
