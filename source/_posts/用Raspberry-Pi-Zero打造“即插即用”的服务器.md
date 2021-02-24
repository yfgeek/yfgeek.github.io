---
title: 用Raspberry Pi Zero打造“即插即用”的Web服务器
date: 2017-01-12 02:04:44
tags: Raspberry
categories: Rasberry
---


虽然Raspberry Pi Zero只有4.5英镑，非常便宜，但确实具有局限性，由于缺少网口、WiFi，功能比较单一，可曾设想，用Raspberry Pi Zero打造一个完美的“即插即用”的Web服务器？

![](/content/images/zeroweb/0.jpg)

何为即插即用？

类似于优盘一样，插上电脑，等待几秒，直接识别为网卡，直接输入树莓派的网址，连接就是自己的网站。

有什么用？

实现本地web本地化，随着前端、后端技术的发展，web应用越来越多，但是web应用受制于网络的限制，一旦没有网络，就没有用武之地。

那么为什么不把web本地化呢？以后拿着“即插即用”的服务器，到任何电脑上，都能随时办公。

# 安装

我们可以把树莓派虚拟为网卡，然后在内网连接它。

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


# 配置

1.ssh连接raspberrypi.local，默认密码为raspberry
```bash
pi@raspberrypi.local
```

2.通过直接运行``raspi-config``命令

你需要做
* 容量扩展
* 更改系统时区
* 更改系统密码

3.同时你需要通过执行``sudo passwd root``更改root密码

4.在本机设置代理

首先，设置本机名为Mac，在本机搭建一个squid，为了使树莓派安装web server，以后日常使用不需要。

```
brew update
brew install squid
```
5.然后下载[SquidMan](http://squidman.net/squidman/)，安装。
在本机开启端口为8080的HTTP代理服务，并把本机名称更为Mac。


6.进入树莓派的shell，输入：
```bash
cd /etc/apt/apt.conf.d
sudo vi 50raspi
```
最后一行加入一句话：
```
Acquire::http::Proxy "http://Mac.local:8080";
```
保存

7.继续输入``apt-get update``查看是否有网络。

8.安装`lighthttpd+mysql+php`

输入：

```bash
sudo apt-get install lighttpd mysql-server php5-cgi php5-mysql
```

等待安装完毕后，修改`php.ini`设置

```
sudo nano /etc/php5/cgi/php.ini
```
去掉这一条注释的`;`
```
;cgi.fix_pathinfo = 1
```
随后修改``lighttpd.conf``
```
sudo nano /etc/lighttpd/lighttpd.conf
```

在`server.modules`中增加`fastcgi`
```
server.modules = (
"mod_access",
"mod_alias",
"mod_compress",
# "mod_redirect",
"mod_fastcgi",
# "mod_rewrite",
)
```
最后添加
```
fastcgi.server = ( ".php" => ((
    "bin-path" => "/usr/bin/php5-cgi",
    "socket" => "/tmp/php.socket"
)))
```

重启lighttpd服务
```
sudo /etc/init.d/lighttpd restart
```

通过scp协议，上传一个``index.php``文件
```
<?php
phpinfo();
?>
```
删除默认的``index.htm``文件，打开网站``http://自己树莓派名字.local``
查看是否成功。

![](/content/images/zeroweb/1.png)

不错，接下来的事情，就是把自己的源代码传到树莓派上，不过多叙述。

# 多说一句

从此以后，你只需要带上你的树莓派Zero就可以随时随地的打开自己的私有web服务。但是，树莓派Zero的性能，确实不行，建议跑一跑小型的程序，不要跑太大的程序。

![](/content/images/zeroweb/2.png)

至于应用嘛，其实做什么都可以的。