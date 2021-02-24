---
title: 树莓派搭建ss服务器
date: 2016-09-13 23:38:35
tags: Raspberry
categories: Linux

---
# 简介
树莓派打算长期放在家里了，为了方便我身居海外还能方便的听上网易云音乐，顺利的看上国内的优酷爱奇艺，打算在树莓派上搭ss服务器

# 为什么不用pptp vpn
这是个好问题，实际上我已经搭建了pptp vpn，只是连不上，大概是因为国内运营商中国电信做了端口屏蔽限制，目前发现已经屏蔽的端口有80和PPTP的端口。

# 一键配置脚本
```
wget http://mirrors.linuxeye.com/oneinstack.tar.gz
tar xzf oneinstack.tar.gz
cd oneinstack
./shadowsocks.sh install
```
漫长的等待结束就好了

# 慢慢配置
## 安装Pip
```
apt-get install python-pip python-gevent python-m2crypto
```

## 安装Shadowsocks
```
pip install shadowsocks
```

## 创建Shadowsocks配置文件
配置文件 `/etc/shadowsocks.json` 内容如下：
```
{
    "server":"0.0.0.0",
    "server_port":8388,
    "local_address": "127.0.0.1",
    "local_port":1080,
    "password":"密码",
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open": false,
    "workers": 1
}
```
## 启动服务
```
nohup ssserver -c /etc/shadowsocks.json &
```

## 设定为自动启动的方法
写入 `nohup ssserver -c /etc/shadowsocks.json &` 到 /etc/rc.local 文件的 exit 之前。
