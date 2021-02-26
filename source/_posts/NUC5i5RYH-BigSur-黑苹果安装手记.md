---
title: NUC5i5RYH Big Sur 黑苹果安装教程
date: 2021-02-26 18:16:30
tags: Mac
categories: Mac
banner_img: /banners/bigsurnuc.png
---


> 本文内容仅供学习，需要十分清楚的是黑苹果其实是一个具有潜在盗版性质的行为，因为它违反了 EULA 法案(EULA)。 所以本教程只是以安装黑苹果为一个引线，进而介绍和分享关于苹果操作系统的相关知识和系统的一些安装方法，不参与任何商业活动，也不允许任何人使用本文章搜集和创作的资料参与任何商业活动。如果你想要开始黑苹果技术，我们希望你是抱着一个极客的心态来的，并且将这些技术应用到其他相关领域的学习和工作中去。

2021年年度更新Big Sur版本，本次版本更换Clover到OpenCore，对如下内容进行更新。

本人全部过程参考以下教程，为减少您的坑，我已经把我调教好的配置文件和驱动上传至GitHub。

# 效果图

![](/content/images/bigsur/snap.jpg)


* Display Port 完美 (4K)
* HDMI 完美
* 音频输出 完美
* 睿频 完美
* 休眠 完美
* USB3.0 完美
* WIFi 完美
* 蓝牙 完美

# 材料

* 至少8GB U盘一枚
* [macOS Big Sur Installer 镜像](https://blog.daliansky.net/macOS-BigSur-11.2.1-20D75-Release-version-with-OC-0.6.6-and-Clover-5129-and-PE-original-image.html)
* 本机硬盘 GPT分区 至少预留一个空分区 如果全盘安装，请将硬盘清空
* [TransMac(dmg文件打开工具)]([http://www.pc6.com/softview/SoftView_91686.html](http://www.pc6.com/softview/SoftView_91686.html))
* DiskGenius
* 一个winpe盘(用于处理紧急情况，可选，也可以使用备用电脑)
* [为您调教的NUC5i5RYH专用配置文件](https://github.com/yfgeek/NUC5i5RYH-DSDT-Patch/tree/master/BigSur%20OpenCore)

# 准备安装U盘

确保上述到位后，使用TransMac打开下载好的dmg文件。具体操作步骤如下：

1. 把 U 盘插入电脑，然后再打开之前安装好的 TransMac 软件，找到窗口左侧中出现的U盘；
2. 将 U 盘格式为 Mac 系统下的磁盘格式，选中 U 盘并右键选择“Format Disk for Mac”选项，保持默认的 Mac OS Extended HFS+ 即可；
3. 随后软件会提示我们是否要将此 U 盘格式为 Mac 磁盘格式，点击 OK 按钮继续。接着请等待软件将 U 盘格式为 Mac 磁盘格式；
4. 当 U 盘被格式化完成以后，选中 U 盘并点击右键选择“Restore with Disk image”选项，选择了一个 Mac OS的Mac系统镜像；
5. 此时软件会提示我们 U 盘上数据将会被清除，点击“Yes”按钮继续。等待软件将 Mac 系统的镜像恢复到 U 盘里。
6. 当出现“Restore Complete”的时候就代表MacOS的U盘启动盘制作完成了。

![](/content/images/hackintosh/1.gif)

刻录好后，别急，使用DiskGenuis打开U盘刻录好的EFI分区，打开[为您调教的NUC5i5RYH专用配置文件](https://github.com/yfgeek/NUC5i5RYH-DSDT-Patch/tree/master/BigSur%20OpenCore)，将EFI目录下的目录替换EFI分区内的目录。

大功告成，重启。

# 进入安装阶段

 开机，F2进入BIOS，设置如下

- Devices->Onboard Devices->Legacy Device Configuration,  "Serial Port"  设置为 disabled
- Boot->Boot Configuration,  "Network Boot" 设置为 disabled
- Power->Secondary Power Settings, "Wake on LAN from S4/S5", 设置为 "Stay Off"

- Devices->USB, "xHCI Mode" 设置为 "Smart Auto"
- Devices->Video, "IGD Minimum Memory" 设置为 64mb
- Devices->Video, "IGD Aperture Size" 设置为 256mb
- Boot->Secure Boot, "Secure Boot" 设置为 disabled
- Security->Security Features, "Execute Disable Bit" 设置为 enabled

开机，按选择U盘启动，进入Clover界面，选择优盘启动。

稍等片刻，即可进入安装界面。

![](/content/images/hackintosh/3.jpg)

![](/content/images/hackintosh/5.jpg)

选择磁盘工具，选择要安装的磁盘，将其格式化为APFS文件系统。

随后的事情就很简单了，顺序安装软件即可。

# 第一次重启

进度条走完后，会自动重启，此时非常重要，重启后一定要选择macOS install

然后进入

# 第二次重启

重启后选择**Boot macOS from 硬盘名**，本次重启会像安装iOS一样，走黑苹果下的进度条，走完即可。耐心等待。

# 进入桌面

不出意外的话，就能进入正常的macOS第一次安装后的桌面，进入常规设置后。

安装[OpenCore Configurator](https://gitee.com/shuiyunxc/OpenCore)，便于后续维护，同时挂载EFI 分区。

```bash
# 一、进入Single User模式：

sudo -s

# 二、在Volumes下创建EFI文件夹：

mkdir /Volumes/EFI

# 三、确定分区：

diskutil list

# 一般的EFI分区会在disk0s1，

# 四、挂载分区：

mount -t msdos /dev/disk0s1 /Volumes/EFI

# 五、如果使用结束卸载分区：

diskutil unmount /dev/disk0s1
```

打开之前[为您调教的NUC5i5RYH专用配置文件](https://github.com/yfgeek/NUC5i5RYH-DSDT-Patch/tree/master/BigSur%20OpenCore)，将EFI目录下的目录替换加载好的EFI分区内的目录。


![](/content/images/bigsur/snap.jpg)

重启，大功告成！
