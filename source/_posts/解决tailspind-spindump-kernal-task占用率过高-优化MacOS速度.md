---
title: '优化Mac OS速度 解决tailspind、spindump、kernel_task问题'
date: 2018-07-18 11:08:31
tags: Mac
categories: Mac
---

最近安装了Mojave，发现卡了不少，其实问题不在系统，在于beta版bug比较多，有两个进程是专门用于生成应用程序调试快照的，对于我们普通用户来讲，没有什么用处，关闭，可以大大的提升系统的速度。

# 关闭 tailspind 与 spindump

第一步，重启，开机的时候按住`Command+R`

第二步，选择终端，并且输入 `csrutil disable`，这是为了关闭SIP保护机制

第三步，重启，进入系统，在终端中输入

```bash
sudo launchctl unload -w /System/Library/LaunchDaemons/com.apple.spindump.plist
sudo launchctl unload -w /System/Library/LaunchDaemons/com.apple.tailspind.plist
sudo mv /System/Library/LaunchDaemons/com.apple.spindump.plist /System/Library/LaunchDaemons/com.apple.spindump.plist.bak
sudo mv /System/Library/LaunchDaemons/com.apple.tailspind.plist /System/Library/LaunchDaemons/com.apple.tailspind.plist.bak
```

第四步，重启，完成

# kernel_task 占用率过高

在日常使用中，我们总能发现kernel_task占用很高的内存和CPU，通过以下方法可以解决：

第一步，重启，开机的时候按住`Command+R`

第二步，选择终端，并且输入 `csrutil disable`，这是为了关闭SIP保护机制

第三步，重启，进入系统，在终端中输入

```bash
sudo mv /System/Library/Extensions/IOPlatformPluginFamily.kext /System/Library/Extensions/IOPlatformPluginFamily.kext.bak
```

第四步，重启，完成

# 开启SIP

之前的操作，我们无形中关闭了SIP机制，为了使系统更安全，我们需要

第一步，重启，开机的时候按住`Command+R`

第二步，选择终端，并且输入 `csrutil enable`，重新开启mac OS保护机制

# 参考

https://www.macobserver.com/tips/how-to/disable-tailspind-spindump-macos-cpu-speed/

https://blog.csdn.net/liumx2007/article/details/77164795