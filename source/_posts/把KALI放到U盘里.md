---
title: 把KALI放到U盘里
tags: 'Linux'
permalink: ba-kalifang-dao-upan-li
id: 16
updated: '2016-08-25 19:29:38'
date: 2016-08-25 19:26:45
---

步骤1：进入 bios设置U盘启动，保存并启动电脑

步骤2：在启动选择画面中，选第一个“kali linux(686-pae)”。
<!-- more --> 
步骤3：进入kali桌面后 打开命令行，输入gparted，在弹出的窗口程序的右上角选择对应你U盘容量的磁盘。把第二个分区unmount后，将此区格式化成 ext4文件系统，同时把分区名(Label)改成 persistence ，同时记下分区列表里面第二个分区的分区名 例如我的是 /dev/sdb2。

关掉gparted ，在命令行里输入以下命令：

```bash
mkdir /mnt/usb
mount /dev/sdb2 /mnt/usb
echo "/ union" >> /mnt/usb/persistence.conf
umount /mnt/usb
```