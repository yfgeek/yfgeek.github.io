---
title: Raspberry Pi Zero联网奇妙记
date: 2016-12-21 23:01:26
tags: Raspberry
categories: Rasberry
header-img: "/content/images/zeronet/ras.jpg"
---

说句实话，Zero真的不适合把玩，想让他连上网，太难了。

# 痛点
- 住公寓，有WiFi/Lan登录认证系统
- 公寓的网络动态分配范围很大，很难快速找到树莓派的IP
- 公寓的网络防止扫描端口，有惩罚策略
- 没有显示器 无法查看树莓派Zero的状态
- 树莓派Zero只有一个OTG USB接口
- 树莓派Zero没有内置以太网Lan
- 树莓派Zero没有无线网卡WiFi
- 树莓派Zero没有蓝牙 更没有蓝牙局域网
- 树莓派Zero除了核心部分 一无所有

# 已有装备
- 树莓派Zero
- USB HUB 分线器
- USB 以太网网卡
- USB WIFI (芯片 8188EU)
- 路由器
- 网线一根
- MacBook Pro一台

# 已有环境
- Raspbian系统
- vncserver

# 解决方案
## 整体思路

![](/content/images/zeronet/0.png)

其中 USB HUB USB网卡 临时路由器 PC 是临时之用 配置一旦完成 抛弃这些设备直接插上WiFi自动联网。

![](/content/images/zeronet/1.jpg)

## 去掉之前的USB网卡模式
把sd卡取出，插入电脑

在``config.txt``下删除
```
dtoverlay=dwc2
```
在``cmdline.txt``下删除
```
modules-load=dwc2,g_ether
```

## 安装WiFi驱动

之前遇到过很多坑，比如别的内核的WiFi驱动。

首先手动下载

https://dl.dropboxusercontent.com/u/80256631/install-wifi.tar.gz

检查无线适配器版本，同时传到树莓派上

```bash
pi@zero:~ $ sudo ./install-wifi

Your current kernel revision = 4.4.34+
Your current kernel build    = #930

Checking for a wifi module to determine the driver to install.

Your wifi module is Bus 001 Device 003: ID 0bda:8179 Realtek Semiconductor Corp.

And it uses the 8188eu driver.

Checking for a new 8188eu wifi driver module for your current kernel.
The script cannot access Dropbox to check a driver is available.
```
手动下载
https://dl.dropboxusercontent.com/u/80256631/8188eu-4.4.34-930.tar.gz

切换WiFi到树莓派网络
```bash
scp 8188eu-4.4.34-930.tar.gz pi@zero.local:~
```
在树莓派下
```bash
tar xzf  8188eu-4.4.34-930.tar.gz
sudo ./install.sh
reboot
```
然后检查一下 ``lsusb``(这个其实不靠谱) 

然后再检查一下 ``ifconfig``

```bash
wlan0     Link encap:Ethernet  HWaddr 0c:82:68:12:93:8f
          inet6 addr: fe80::4336:2657:c812:f11b/64 Scope:Link
          UP BROADCAST MULTICAST  MTU:1500  Metric:1
          RX packets:0 errors:0 dropped:6 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)
```
果然正确安装了。

## 配置WiFi

但是``wlan0     Link encap:Ethernet``被识别为了以太网，接下来我们继续解决坑。
```bash
sudo iwlist wlan0 scan
```

```bash
  Cell 03 - Address: F0:B0:52:3B:7B:68
                    ESSID:"ASK4 Wireless"
                    Protocol:IEEE 802.11gn
                    Mode:Master
                    Frequency:2.417 GHz (Channel 2)
                    Encryption key:off
                    Bit Rates:144 Mb/s
                    Quality=18/100  Signal level=70/100
                    Extra:fm=0001
```
随后对``wpa_supplicant.conf``文件进行编辑，使之加入到自己的网络里。

请注意，``key_mgmt=NONE``表示没有WiFi密码(这是个查了很久猜得到的宝贵线索)，如果有密码请把这个更改为``psk = "密码" ``

```bash
sudo vi /etc/wpa_supplicant/wpa_supplicant.conf
```

```bash
country=GB
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
network={
        ssid="ASK4 Wireless"
        key_mgmt=NONE

}
```
编辑``/etc/network/interfaces``文件，使wlan0为动态分配获取IP。

```bash
sudo vi /etc/network/interfaces
```
```bash
auto lo
iface lo inet loopback

iface eth0 inet dhcp

auto wlan0
allow-hotplug wlan0
iface wlan0 inet dhcp
wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf


allow-hotplug wlan1
iface wlan1 inet manual
    wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf
```

随后重启我们wlan0

```bash
sudo ifdown wlan0
sudo ifup wlan0
```

如果能搜索到WiFi会自动进行DHCP获取ip地址阶段

```bash
Listening on LPF/wlan0/0c:82:68:12:93:8f
Sending on   LPF/wlan0/0c:82:68:12:93:8f
Sending on   Socket/fallback
DHCPDISCOVER on wlan0 to 255.255.255.255 port 67 interval 8
DHCPDISCOVER on wlan0 to 255.255.255.255 port 67 interval 12
DHCPREQUEST on wlan0 to 255.255.255.255 port 67
DHCPOFFER from 10.80.192.1
DHCPACK from 10.80.192.1
bound to 10.80.200.83 -- renewal in 440 seconds.
```
随后，用``ifconfig``命令查看一下获取的IP地址

```bash
wlan0     Link encap:Ethernet  HWaddr 0c:82:68:12:93:8f
          inet addr:10.80.200.83  Bcast:10.80.207.255  Mask:255.255.240.0
          UP BROADCAST MULTICAST  MTU:1500  Metric:1
          RX packets:0 errors:0 dropped:17 overruns:0 frame:0
          TX packets:0 errors:0 dropped:1 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:4681 (4.5 KiB)  TX bytes:18372 (17.9 KiB)
```

![](/content/images/zeronet/3.png)

再对mac地址进行绑定，再进入vnc，试试看

![](/content/images/zeronet/4.jpg)

哇 可以上网了！

然而悲痛的消息还有呢~

内网隔离

## ngrok TCP转发

这难不倒我，用ngrok就可以解决，正好身在墙外，更加方便。

下载ngrok
```bash
wget https://bin.equinox.io/c/4VmDzA7iaHb/ngrok-stable-linux-arm.zip
unzip ngrok-stable-linux-arm.zip
```
配置开机启动项``vi /etc/rc.local``
在exit 0 前面加入
```bash
/home/pi/ngrok authtoken 你的AUTO
/home/pi/ngrok tcp 22
```
搞定！拔掉USB HUB，关掉路由器，重启！

过了10分钟后，我们的树莓派上线了！

![](/content/images/zeronet/5.png)

用ssh命令连接一下

```bash
ssh pi@0.tcp.ngrok.io -p 13016
```
连接速度超级快，如同本地一样，给ngrok官网点赞。

![](/content/images/zeronet/6.jpg)

## lighttpd 部署

总要搞点事情嘛，部署一个Web服务对于512MB的内存的树莓派Zero不过分吧。

那么什么Web服务最合适呢？脑海第一时间想到轻量级的lighttpd，ngnix我都嫌弃费资源，那么就它了。

```bash
sudo apt-get install -y lighttpd
```

安装完成！目录在``/var/www/html``

写好html，部署上去。

那么如何才能办到同时映射到两个端口呢？ngrok的官方文档有提示。

``nano /home/pi/.ngrok2/ngrok.yml``
```
authtoken: 你的AUTH
region: eu
json_resolver_url: ""
dns_resolver_ips: []

tunnels:
  app-foo:
    addr: 80
    proto: http
    host_header: app-foo.dev
  app-bar:
    addr: 22
    proto: tcp
    host_header: app-bar.dev

```
这样就部署了两个服务，``app-foo``，``app-bar``，不错，开机再启动他们。

```bash
sudo nano /etc/rc.local
```
把之前那句换成
```
/home/pi/proj/ngrok/ngrok start --config /home/pi/.ngrok2/ngrok.yml app-foo app-bar
```
好了，剩下我们要做的就是重启与等待。

![](/content/images/zeronet/7.jpg)

哈哈，上线了，访问一下。

![](/content/images/zeronet/8.png)

这个只有4英镑的树莓派实现联网后花费了：

树莓派：4磅

OTG线：0.86磅

WIFI适配器：人民币25元

储存卡：9磅

谢谢~~观看~~忍耐全文。