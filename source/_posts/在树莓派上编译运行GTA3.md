---
title: 在树莓派上编译运行GTA3
date: 2020-09-28 18:16:30
tags: RaspberryPi
categories: RaspberryPi
banner_img: /banners/gta3.png
---

最近观闻[《GTA3迟迟不推出Switch版本，于是玩家自己动手搞定了》](https://new.qq.com/omn/20200925/20200925A0G6X500.html)，看了一下项目工程，支持Linux、Mac、Windows，想到，何不动手在树莓派上试一试？

# 运行环境

系统：在Raspberry Pi OS 32位、64位均可运行，Ubuntu Core+Mate桌面尝试失败；

GPU：需关闭OpenGL的Fake模式

设备：Raspberry Pi 4

# 编译

开源项目：https://github.com/rwengine/openrw

## 准备编译工具

在命令行中，安装必备工具

```bash
sudo apt install libbullet-dev libsdl2-dev libavcodec-dev libavformat-dev libglm-dev libopenal-dev libboost-dev libboost-filesystem-dev libboost-system-dev libboost-program-options-dev cmake -y
```

## 下载OpenRW

```bash
cd ~
git clone --recursive https://github.com/rwengine/openrw.git openrw
```

## 编译

```bash
mkdir build
cd build
cmake .. -DCMAKE_BUILD_TYPE=Release
cmake --build .
```

### 拷贝GTA3 资源文件

下载原生的GTA3，找到包路径，完全复制到树莓派里

# 配置

```
mkdir ~/.config/OpenRW
cd ~/.config/OpenRW
vim openrw.ini
```

添加如下配置内容

```
[game]
; exe的文件路径
path=/opt/games/Grand Theft Auto 3/
; 游戏语言，只可选american, french, german, italian 和 spanish
language=american

[input]
; Invert mouse-look camera Y
invert_y=0

[window]
; Size of the window
width=800
height=600
; Start in fullscreen
fullscreen=0
```

# 运行

在桌面环境中，进入目录`~/openrw/build/rwgame`

运行编译后的文件 `./rwgame`

美中不足的是，我尝试用`raspi-config`打开OpenGL，却提示`GLXBadFBConfig`。

目测1FPS-2FPS，实验意义大于实际意义。

![](/content/images/gta/gta.png)

视频围观：https://www.bilibili.com/video/BV16K4y187o3/