---
title: iOS 10 iMessage字符崩溃Bug又来了
date: 2017-01-02 03:49:54
tags: Hack
categories: Hack
---

这是一篇本人发表到Freebuf的文章。

# 介绍

近日，黑客@vincedes3发现了一个从iOS 8 到 iOS 10.2.1 b2通用的iMessage字符崩溃Bug，该Bug同样利用了和当年iOS 8的iMessage短信Bug的类似手法，将一段恶意代码发送给受害者，受害者在接收短信后，浏览短信即可中招，此后短信应用陷入死机状态，除非打开修复网页程序，否则再也无法打开短信应用。

![1.png](http://image.3001.net/images/20170102/14833282151664.png!small)

# 利用

1\. 下载利用文件 [点击这里下载](http://vincedes3.com/vincedes3.vcf)

2\. 把它传到 iCloud Drive, 百度云, 或者任何你能够在iPhone上下载到的地方

3\. 打开这个文件，点击分享，再点击 短信图标

4\. 通过iMessage把这个文件传给你的受害者好友

5\. 等待受害者点开短信，他会中招的

6\. 可以通过这个链接来修复 [点我修复](http://vincedes3.com/save.html)

<embed src='http://player.youku.com/player.php/sid/XMTg5NTI3ODE2NA==/v.swf' allowFullScreen='true' quality='high' width='480' height='400' align='middle' allowScriptAccess='always' type='application/x-shockwave-flash'></embed>

[demo视频](http://v.youku.com/v_show/id_XMTg5NTI3ODE2NA==.html#paction)

# 工作原理

在受害者打开短信的时候，触发了大量能够引起短信程序崩溃的字符，当用户浏览该短信的时候，cpu进行了大量的计算直到短信app点不动。当用户关掉短信程序后，再次打开短信程序，系统试图加载上一次内容，依然会触发该bug。

![2.jpg](http://image.3001.net/images/20170102/14833289116916.jpg!small)

TXT版本的触发代码: [http://vincedes3.com/crashtext.txt](http://www.example.com)

HTML版本的触发代码: [http://vincedes3.com/crashtext.html](http://vincedes3.com/crashtext.html)

# 修复

1\. 把这个链接发给受害者手机 [点我修复](http://vincedes3.com/save.html)

2\. 该链接会触发短信的快捷链接，点击打开

3\. 进入发送短信页面

4\. 点击取消

5\. 删掉发送者的短信即可解决bug，请勿再次尝试点入

本文翻译自：[https://vincedes3.com/crash-message-app-iphone/](https://vincedes3.com/crash-message-app-iphone/)