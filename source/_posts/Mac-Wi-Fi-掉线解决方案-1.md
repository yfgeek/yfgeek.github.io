---
title: Mac Wi-Fi掉线解决方案
date: 2016-10-09 15:07:52
tags: Mac
categories: Mac
---

最近连接学校的WiFi老掉线，经过清空WiFi默认配置后，成功。

分享解决方案：

进入目录`/Library/Preferences/SystemConfiguration
`

删除

```
com.apple.airport.preferences.plist
com.apple.network.identification.plist
com.apple.wifi.message-tracer.plist
NetworkInterfaces.plist
preferences.plist
```

重启

解决问题。

转载自：http://blog.chenxiaosheng.com/posts/2014-11-17/mac_osx_yosemite_fix_wifi_problems.html