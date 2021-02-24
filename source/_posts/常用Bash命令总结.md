---
title: 常用Bash命令总结
tags: 'Linux'
permalink: bash
id: 10
updated: '2016-08-06 11:15:29'
date: 2016-08-06 00:36:19
---

自从入了Mac后，感觉敲命令更加方便了，对于我这种半吊子青年，一直号称不会写脚本的前端不是好美工，总结了一些命令，仅供参考，省去了最简单的命令，不过部分也比较简单：

<!-- more --> 

##  日常
* ` ctrl + a ` #命令跳转到句首
* `ctrl + r` #命令搜索模式
* `TAB` #命令自动补全
* `pwd` #显示当前路径
* `tar -cvf mytest.tar mytest/ ` #对mytest目录打包
* `tar -xvf mytest.tar ` #解压tar文件
* `tar -xvf mytest.tar -C /opt/setups/` #解压到特殊目录
* `find / -name *.conf` #查找/下的所有conf文件
* `du -sh ./*` #查看当前文件夹下的空间占用情况
* `more` #分页查看
* `which` #查看目录
* `locate` #快速搜索

## 安全
* `who` #当前登录情况
* `last` #最近登录情况
* `cat /etc/passwd |awk -F \: '{print $1}'` #系统所有用户
* `chkconfig` #启动项检查
* `netstat -lnp` #端口情况
* `uptime` #系统开机多久了
* `sudo iptables -A INPUT -p tcp -m tcp --dport 80 -j ACCEPT ` #允许访问 80 端口

## 一句话命令
脚本中切换用户
```bash
su - test -c "pwd"
```

连接到你服务器端口的ip
```bash
netstat -tun|grep 7890|sort|awk '{print $5}'|cut -d : -f 1|uniq 
```
## 常用功能
## #定时任务
使用-l参数列出crontab文件:
```bash
crontab -l
```
编辑crontab文件
```bash
crontab -e
```
实例1：每1分钟执行一次myCommand
```bash
* * * * * myCommand
```
实例2：每小时的第3和第15分钟执行
```bash
3,15 * * * * myCommand
```
实例3：在上午8点到11点的第3和第15分钟执行
```bash
3,15 8-11 * * * myCommand
```
实例4：每隔两天的上午8点到11点的第3和第15分钟执行
```bash
3,15 8-11 */2  *  * myCommand
```
实例5：每周一上午8点到11点的第3和第15分钟执行
```bash
3,15 8-11 * * 1 myCommand
```
实例6：每晚的21:30重启smb
```bash
30 21 * * * /etc/init.d/smb restart
```
实例7：每月1、10、22日的4 : 45重启smb
```bash
45 4 1,10,22 * * /etc/init.d/smb restart
```
实例8：每周六、周日的1 : 10重启smb
```bash
10 1 * * 6,0 /etc/init.d/smb restart
```
实例9：每天18 : 00至23 : 00之间每隔30分钟重启smb
```bash
0,30 18-23 * * * /etc/init.d/smb restart
```
实例10：每星期六的晚上11 : 00 pm重启smb
```bash
0 23 * * 6 /etc/init.d/smb restart
```
实例11：每一小时重启smb
```bash
* */1 * * * /etc/init.d/smb restart
```
实例12：晚上11点到早上7点之间，每隔一小时重启smb
```bash
* 23-7/1 * * * /etc/init.d/smb restart
```
## #scp
实例1：从远处复制文件到本地目录
```bash
scp root@10.6.159.147:/opt/soft/demo.tar /opt/soft/
```
说明：从10.6.159.147机器上的/opt/soft/的目录中下载demo.tar 文件到本地/opt/soft/目录中

实例2：从远处复制到本地
```bash
scp -r root@10.6.159.147:/opt/soft/test /opt/soft/
```
说明：从10.6.159.147机器上的/opt/soft/中下载test目录到本地的/opt/soft/目录来。

实例3：上传本地文件到远程机器指定目录
```bash
scp /opt/soft/demo.tar root@10.6.159.147:/opt/soft/scptest
```
说明：复制本地opt/soft/目录下的文件demo.tar 到远程机器10.6.159.147的opt/soft/scptest目录

实例4：上传本地目录到远程机器指定目录
```bash
scp -r /opt/soft/test root@10.6.159.147:/opt/soft/scptest
```
说明：上传本地目录 /opt/soft/test到远程机器10.6.159.147上/opt/soft/scptest的目录中

