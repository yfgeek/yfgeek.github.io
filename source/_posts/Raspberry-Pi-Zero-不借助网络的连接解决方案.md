---
title: Raspberry Pi Zero 无需键盘网络 配置教程
date: 2016-12-17 21:58:26
tags: Raspberry  
categories: Raspberry  
---

我大概是个Raspberry Pi粉丝，到了英国后总感觉不买点树莓派的东西亏，可是之前已经在中国买了树莓派3了，于是决定买点特别的，做点有意思的事情。

上个月，鉴于我在英国的巨大地理优势的缘故，买了一个4磅的Raspberry Pi Zero，不过网络上关于树莓派Zero的文章真的甚少，因为实在太难原价买到了。

<!--more--> 

请注意，不要认为所谓的4磅很便宜，毕竟，我还多掏了2.50磅的邮费。树莓派Zero在中国并不便宜，因为树莓派Zero的产能有限，每周只能供很少的货，买树莓派Zero就像抢12306一样，我抢了两周，也正是因为这个原因，树莓派Zero在亚马逊的黄牛卖家卖20磅，淘宝卖120元左右，实际上原价只有40元左右。

# 信仰充值

滴！信仰充值成功！

![](/content/images/raspizero/mail.jpg)

没有错，你没有看错，就是这么小，但是小也有小的坏处，没有USB口，没有Wi-Fi，没有网线接口，似乎什么都没有用。

* 1Ghz, Single-core CPU
* 512MB RAM
* Mini HDMI and USB On-The-Go ports
* Micro USB power
* HAT-compatible 40-pin header
* Composite video and reset headers

不过值得一提的是，USB On-The-Go接口也可以用来供电。

# 发车

网络上关于树莓派Zero如何连接配置大概有以下几种：

- OTG口转USB 接 USB以太网适配器
- OTG口转USB 接 USB Wi-Fi
- OTG口转USB 接 USB键盘 且 用HDMI接显示器
- GPIO接以太网适配器

但是以上，都太麻烦了，对不对？

那么关键问题就来了，如何才能在 没有网口、没有Wi-Fi、没有显示器、没有鼠标、没有键盘的情况下 配置可爱的树莓派Zero呢？

我们 可以 把 树莓派 虚拟为 网卡，然后在内网连接它。

1.下载完整版 Raspbian Jessie 或 Raspbian Jessie Lite 系统，并且刷到你的SD Card里，Windows用Win32 Disk Imager， Mac/Linux 用命令，如下：

```bash
diskutil list
diskutil unmountDisk /dev/SD卡的标号 #如disk2
cd /Users/ivan/Downloads/2016-11-25-raspbian-jessie #进入目录
sudo dd bs=1m if=2016-11-25-raspbian-jessie.img of=/dev/rdisk2
```
2.刷好系统后编辑`config.txt`文件，在最后一行下，另起一行添加：
```bash
dtoverlay=dwc2
```
保存文件

3.最后，打开`cmdline.txt`文件。
找到单词`rootwait`，在其之后添加：

```
modules-load=dwc2,g_ether
```

4.添加一个文件名为`ssh`到根目录下，内容为空


5.把SD Card放到树莓派Zero，用安卓数据线与电脑连接，USB口接OTG口，等待60-90s，电脑会发现网卡并且自动配置ip地址。

![](/content/images/raspizero/1.png)

6.Ping 一下raspberrypi.local看是否能通

```
PING raspberrypi.local (169.254.70.245): 56 data bytes
64 bytes from 169.254.70.245: icmp_seq=0 ttl=64 time=0.570 ms
64 bytes from 169.254.70.245: icmp_seq=1 ttl=64 time=0.338 ms
64 bytes from 169.254.70.245: icmp_seq=2 ttl=64 time=0.316 ms
64 bytes from 169.254.70.245: icmp_seq=3 ttl=64 time=0.344 ms
```

7.ssh连接raspberrypi.local，默认密码为raspberry
```bash
pi@raspberrypi.local
```

# 配置

因为没有网络，只能通过下载离线安装文件，传输到树莓派内，再在树莓派里安装。

## 配置树莓派
通过直接运行``raspi-config``命令

你需要做
* 容量扩展
* 更改系统时区
* 更改系统密码

同时你需要通过执行``sudo passwd root``更改root密码

## 安装vnc-server

首先，去[VNC官网](https://www.realvnc.com/download/vnc/raspberrypi/)下载最新版本的vnc-server

用scp命令传到树莓派上

```bash
scp /Users/ivan/Downloads/VNC-5.3.2-Linux-ARM-DEB.tar.gz  pi@raspberrypi.local:~
```
在树莓派shell下，安装VNC
```
tar xvf VNC-5.3.2-Linux-ARM-DEB.tar.gz
sudo dpkg -i VNC-Server-5.3.2-Linux-ARM.deb
```

![](/content/images/raspizero/2.png)


```
vncpasswd -user  # 设置连接密码
vncserver  # 启动vncserver
```

连接raspberry.local:1测试成功

![](/content/images/raspizero/3.png)

## 设置代理
本来想着，既然把它虚拟为网卡了，能否NAT网络给他让他上网？事实上，是失败的。

那么我们有一个临时解决方案，就是设置代理

首先，在本机搭建一个squid

```
brew update
brew install squid
```
然后下载[SquidMan](http://squidman.net/squidman/)，安装。
在本机开启端口为8080的HTTP代理服务，并把本机名称更为Mac。

进入树莓派shell，输入：
```bash
cd /etc/apt/apt.conf.d
sudo vi /etc/apt/apt.conf.d
```
最后一行加入一句话：
```
Acquire::http::Proxy "http://Mac.local:8080";
```
保存

apt-get update一下，怎么样是不是能装软件了？

# 感想

这块小板子，我太喜欢了，好精致

# 参考
https://gist.github.com/gbaman/975e2db164b3ca2b51ae11e45e8fd40a#file-howtootgfast-md


