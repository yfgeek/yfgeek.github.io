---
title: >-
  +[__NSPlaceholderDate initialize] may have been in progress in another thread
  when fork() was called. 解决方法
date: 2018-09-11 10:59:03
tags: Mac
categories: Mac
banner_img: /banners/troubleshooting.png
---

最近在用python3写一个多线程工具，发现频繁报错：

```bash
objc[27880]: +[__NSPlaceholderDate initialize] may have been in progress in another thread when fork() was called.
objc[27880]: +[__NSPlaceholderDate initialize] may have been in progress in another thread when fork() was called. We cannot safely call it or ignore it in the fork() child process. Crashing instead. Set a breakpoint on objc_initializeAfterForkError to debug.
```

一直以为是代码的问题，不断的检查代码，其实是macOS的High Sierra以上的操作系统对多线程的限制，别无其它原因。

## 临时调试方法

在终端中输入 ``export OBJC_DISABLE_INITIALIZE_FORK_SAFETY=YES``

不要关闭当前终端，再次运行python程序

## 解决方法

打开终端 输入

```bash
cd ~
sudo vim .bash_profile
```

在最后一行加入

```
export OBJC_DISABLE_INITIALIZE_FORK_SAFETY=YES
```

重启终端就解决了。