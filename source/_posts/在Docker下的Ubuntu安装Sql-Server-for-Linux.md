---
title: Docker下配置SQL Server for Linux
date: 2016-11-22 14:44:06
tags: 
- Docker 
- SQLserver
categories: Docker
---

# 前言

微软已经不再是以前的那个微软了，如果微软SQL Server早开源几年，估计现在数据库方面就没有Oracle什么事情了。

Canonical 技术主管 Dustin Kirkland 表示：「微软和 Canonical 继续在 Windows 世界和 Linux 世界之间建立桥梁。 SQL Server on Ubuntu 便是这种趋势的又一示例。」

<!--more -->
![](/content/images/sqldocker/micosoft.png)
# 配置要求

* Docker版本在 1.8 以上
* 至少 4GB 硬盘空间
* 至少 4GB 内存

![](/content/images/sqldocker/0.jpg)

# 配置
## 拉取docker镜像
```
docker pull microsoft/mssql-server-linux
```
## 运行docker镜像

``YourStrong!Passw0rd`` 为你的密码

``1433`` 为对外端口
```
 docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=<YourStrong!Passw0rd>' -p 1433:1433 -d microsoft/mssql-server-linux
```
🌰 例子
```
sudo docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=<YourStrong!Passw0rd>' -p 1433:1433 -d microsoft/mssql-server-linux
```


## 设置 宿主机保存数据目录(暂不支持)

``<host directory>`` 为目录

```
docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=<YourStrong!Passw0rd>' -p 1433:1433 -v <host directory>:/var/opt/mssql -d microsoft/mssql-server-linux
```

## 查看容器的ip(调试用)
```
docker inspect --format='{{.NetworkSettings.IPAddress}}' 容器id
```
172.17.0.2

# 连接
用户名是SA

密码是用户自己设置的

![](/content/images/sqldocker/1.jpg)

连接成功！

![](/content/images/sqldocker/2.jpg)

# 最后

内存占用情况

![](/content/images/sqldocker/3.jpg)


我们一起默念** 微软大法好 ** ** 微软大法好 ** ** 微软大法好 ** 。

谢谢微软为开源世界做的一切！

# 参考
https://docs.microsoft.com/zh-cn/sql/linux/sql-server-linux-setup-docker

