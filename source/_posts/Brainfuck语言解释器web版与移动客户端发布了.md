---
title: Brainfuck语言解释器Web版与移动客户端版 beta
date: 2017-11-30 22:27:24
tags: Brainfuck
categories: Brainfuck React-Native React
---

其实不是多难的东西，有了上文[快速撸出一个Brainfuck语言解释器](http://blog.yfgeek.com/2017/11/30/brainfuck/)的核心解释器源码做基础，做两个客户端版本并不难。

用React+Redux撸了一个Web版，一个移动版。两者都尚未完成，但是在同时进行。

用React persist使数据持久化，也就是说即使你关闭了浏览器，下次不清空缓存的话，程序依然会为你保存。

# 开源

开源地址如下：

Beef(移动版): https://github.com/yfgeek/Beef-react-native

BrainfuckIDE(Web版):https://github.com/yfgeek/BrainfuckIDE

# 图鉴

![](/content/images/brainfuck/snap.png)

![](/content/images/brainfuck/ios.png)