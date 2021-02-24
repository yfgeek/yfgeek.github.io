---
title: 实战树莓派安装Windows XP
date: 2017-10-29 15:36:24
tags: Raspberry
categories: Raspberry
---

# 序

记得小时候，家里的电脑一直安装着Windows 98，天天骂windows难用，虽然没有别的最新的操作系统可以用，也看不出来Windows 98的设计风格有多么古朴，只是bug奇多，动不动就崩溃，系统级别的崩溃，蓝屏，甚至直接坏掉再也无法开机。

直到有一天，偶然的机会，姐姐家里新买来的电脑，过去一看，大不一样，蓝色的界面，巨好看的任务栏、窗体样式，却不知道什么系统，感觉像是windows 却觉得清晰稳定了许多。

后来，我逐渐的了解到，这个系统的名字叫Windows XP，那是2002年，漂亮的界面，清脆优雅的提示音，可爱的图标，让我永远无法忘怀。

回家后，求人把家里的电脑装成Windows XP，却被告知电脑内存只有256MB，硬盘只有30GB，cpu只有800MHZ的配置装的话可能会很卡。无奈之下，我也只好选择接受现实。

有时候，人越没有却得不到的东西，往往是美好的。一见钟情的Windows XP甚至到做梦都会梦到，能梦到Windows meida player、Windows XP之类的把玩，虽然梦里的情节似乎有些不对，但是那种兴奋的感觉，至今无法忘怀。

朝思暮想，什么时候能够玩一玩这个系统，那个时候互联网并不发达，并非是没有网络，只是网络搜不到你想搜的东西，也不是任何一个东西都能在网络上下载到，你说：用迅雷呗？不不不，那个时候没有迅雷，只有——网络蚂蚁。

用Windows 98是痛苦的，经常崩溃甚至需要重新安装系统，我有没有想过过渡产品，比如Windows 2000，对的，有的，其中的过程中我装过几次，但是Windows 2000给我的感觉不是很理想。

2003年，直到有一天，忍无可忍的我，去盗版盘零售店，花了10块钱买了一张Windows XP SP2的安装光盘，我的幸福人生从此开始——

时至今日，我仍然十分喜欢Windows XP，尽管已经过去14年之久，人很难忘记短暂的美好的东西。

# 安装

下面回归正文，本文写写如何用树莓派 3（Raspberry Pi 3）安装Windows XP。

其实这是一个伪命题，arm架构怎么能够安装Windows XP呢？充其量应该算是模拟，没错，答题思路就是在系统上安装虚拟机，用arm虚拟机模拟x86架构安装Windows XP。

其实方法很简单，进入终端，首先：

```shell
sudo apt-get update
```

确认无误后，我们开始安装qemu和kvm：

```shell
sudo apt-get install qemu-kvm qemu virt-manager virt-viewer libvirt-bin
```

网速快的话，应该很快就装完了，这时候一定要重启电脑，否则会提示 `libvirt`未启动：

![](/content/images/rasp-xp/1.jpg)

重启后，[下载精简版XP MicroXP](http://www.mediafire.com/?ynv5hvh7pmt) 密码：warez-bb，当然你愿意安装完整版，也不是不可以。只要下载好并且把iso文件复制到系统里即可。

接下来的事情很简单，创建虚拟机：

应用程序-系统工具-虚拟系统管理器

随后：文件-新建虚拟机

弹出来的对话框里，配置系统架构为x86_64，内存为256mb，所占空间按照自己比例分配，建议1.5GB-2GB。

然后开始启动就可以安装了，安装过程大概需要3-4个小时。

# 图鉴

以下是一些图鉴，敬请欣赏。

![](/content/images/rasp-xp/2.jpg)

![](/content/images/rasp-xp/3.jpg)

![](/content/images/rasp-xp/4.jpg)

![](/content/images/rasp-xp/5.jpg)

![](/content/images/rasp-xp/6.jpg)

![](/content/images/rasp-xp/7.jpg)

![](/content/images/rasp-xp/8.png)

![](/content/images/rasp-xp/9.png)

![](/content/images/rasp-xp/10.png)

![](/content/images/rasp-xp/11.png)

本文谨纪念美好的童年时代。