---
title: 用HomeAssistant + HomeBridge树莓派构建智能家居中枢
date: 2018-02-23 15:45:03
tags: Raspberry
categories: Raspberry
---

 好久没有更新文章啦，最近接了一个私活，也是人越来越懒，懒得更新。

想着不行必须得更新一篇，那就写写最近构建的树莓派吧，话不多说，先上图。

![](/content/images/rasp-home/1.jpg)

# 需要的原料

* 树莓派
* Hass.io或Hassbian或Raspbian系统
* DHT22传感器
* 尽可能多的智能设备

# Home Assistant

Home Assistant 是一个好系统，可以这么说，Home Assistant是一个包装好的智能家居控制系统的中心，在2018年看，可扩展性强，社区强大。

我建议安装Hassbian系统，安静纯粹，安装过程如下。

1. [下载最新的Hassbian景象](https://github.com/home-assistant/pi-gen/releases/latest) (364 MB)

2. 用 [Etcher](https://etcher.io/) 刷入你的树莓派储存卡。

3. 如果你是Wi-Fi链接，请先配置你的网络连接，重新插入sd卡，在根目录下，建立`wpa_supplicant.conf`文件，修改内容并保存。

   ```json
   country=SE
   ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
   update_config=1

   network={
       ssid="YOUR_SSID"
       psk="YOUR_PASSWORD"
   }
   ```

   ​

4. 插入SD卡，开机等待，刷新http://hassbian.local:8123/，直到出现页面。

小提示

* Etcher一定要使用最新版1.3.1，旧版本可能导致无法开机
* 不要相信官网所说的等待五分钟，由于中国的网络环境，很可能需要等待一小时到五小时，都是正常的，只要没有出现网页，就不要进入ssh
* 出现页面后，可以ssh链接进页面，用户名是pi，密码是raspberry，用passwd重置密码。
* 如果超过五个小时没有页面显示，这时候你再输入``sudo systemctl start install_homeassistant.service``重试。

![](/content/images/rasp-home/2.png)

# HomeBridge

HomeBridge可以让你的Hassbian支持iOS的Homekit，所以，这个插件一定要安装。

安装方法如下：

1. ssh进入你的树莓派

2. 安装node

   ```bash
   sudo apt-get update
   curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash 
   sudo apt-get install -y nodejs
   sudo apt-get install -y build-essential
   ```

3. 安装avahi

   ```bash
   sudo apt-get install libavahi-compat-libdnssd-dev
   ```

4. 安装homebridge

   ```bash
   sudo npm install -g --unsafe-perm homebridge
   ```

好了，homebridge安装好后，我们输入homebridge试试可不可以运行？

![](/content/images/rasp-home/3.png)

看到这样的二维码，我们就可以打卡iPhone的家庭.app，然后扫描二维码，添加我们的树莓派中继。

# 温度和湿度传感器

我们添加一个温度和湿度，接线方式

接线需要并联一个10K电阻

```
DHT22有3个脚,VCC,DATA,GND

VCC,接 3.3V,PIN 01

DATA,接 GPIO04,PIN 07

GND,接地,PIN 09

其中DATA和VCC之间用10k电阻相连

```

[![img](https://raw.githubusercontent.com/yfgeek/rpi-TempRuntime/master/images/5.png)](https://github.com/yfgeek/rpi-TempRuntime/blob/master/images/5.png)



随后，执行以下代码

```bash
git clone https://github.com/NorthernMan54/homebridge-dht
cd homebridge-dht
sudo apt-get update
sudo apt-get install pigpio python-pigpio python3-pigpio
sudo cp dht22 /usr/local/bin/dht22
sudo chmod a+x /usr/local/bin/dht22
dht22
```

如果看到显示正常的温度，如

```bash
pi@hassbian:~ $ dht22
0 25.5 C 14.5 %
```

## 智能电视

因为我家有一台三星的智能电视，也可以用homebridge控制，在npm上找到了相应的包，迅速安装，迅速安装

```bash
npm install -g homebridge-samsungtv2016
```



# 配置文件

随后我们配置homebridge

```bash
cd ~
cd .homebridge
vi config.json
```

编辑内容：

```json
{
    "bridge": {
        "name": "Yifan Home",
        "username": "CC:22:3D:E3:CE:30",
        "port": 51826,
        "pin": "031-45-154"
    },

    "description": "Yifan Home",

    "accessories": [
        {
           "accessory": "Dht",
           "name": "dht22",
           "name_temperature": "Temperature",
           "name_humidity": "Humidity",
           "service": "dht22",
           "gpio": "4"
        },
        {
                        "accessory": "SamsungTV2016",
                        "name": "Bedroom TV",
                        "ip_address": "192.168.1.8",
                        "mac_address": "84:A4:66:F1:C7:09"
                }
    ],

    "platforms": [
        ]
}
```

最后，我们打上`homebridge`，打开手机的家庭.app，扫描二维码，添加设备，大功告成！



小提示：

* 如果之前添加错误，想重新配置，请进入`~/.homebridge`文件夹删除`accessories`和`persist`目录即可重新配置。



# 参考

https://home-assistant.io/docs/installation/hassbian/installation/

https://www.jianshu.com/p/ec79b2711bd5

http://www.instructables.com/id/RPIHomeBridge-TemperatureHumidity-Sensor/