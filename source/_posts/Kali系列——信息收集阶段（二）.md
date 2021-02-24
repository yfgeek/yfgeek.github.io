---
title: Kali系列——信息收集阶段（二）
tags: 'Hack'
id: 8
updated: '2016-08-05 21:13:14'
date: 2016-08-01 23:20:17
---
<!-- more --> 
## fierece
暴力破解dns解析
```bash
root@kali:~# fierce -dns dbstye.net

Trying zone transfer first...

Unsuccessful in zone transfer (it was worth a shot)
Okay, trying the good old fashioned way... brute force

Checking for wildcard DNS...
Nope. Good.
Now performing 2280 test(s)...
```
应用字典

```bash
root@kali:~# fierce -dns dbstye.net -wordlist myWordlist.txt
```

## 社工库
部分需翻墙

* http://www.xiumima.com/

* http://cha.hxsec.com/

* http://findmima.com

* http://163.donothackme.club/

* http://www.rallyindonesia.com/