---
title: 记一次MySQL无法启动及修复经历
date: 2017-08-18 20:27:29
tags: Web
categories: Web
---

记得有次本地的MySQL无法启动，网上说，去删掉InnoDB日志就行，我就傻乎乎的去删掉了InnoDB相关的文件，果然，没有任何问题，正常启动了。

可是谁曾想，过了几天，故障复现了，我就按部就班的又去删除，结果，又解决了。

可是，现在棘手的问题又出现了，数据库，彻底的挂掉了。

```ini
[ERROR] InnoDB: Attempted to open a previously opened tablespace
2017-08-18 20:07:04 140736493900736 [ERROR] InnoDB: Attempted to open a previously opened tablespace. Previous tablespace blockvotes/vote uses space ID: 7 at filepath: ./blockvotes/vote.ibd. Cannot open tablespace phpmyadmin/pma__pdf_pages which uses space ID: 7 at filepath: ./phpmyadmin/pma__pdf_pages.ibd
2017-08-18 20:07:04 7fffc4b9d3c0  InnoDB: Operating system error number 2 in a file operation.
InnoDB: The error means the system cannot find the path specified.
InnoDB: If you are installing InnoDB, remember that you must create
InnoDB: directories yourself, InnoDB does not create them.
InnoDB: Error: could not open single-table tablespace file ./phpmyadmin/pma__pdf_pages.ibd
```

按照网上的说法，没救了，因为第一次删除了InnoDB的``ibdata1``文件，这个文件是共享表空间，是不可以随便乱删的。

可是想着我的数据库里还存着很多东西，不能就这样放弃。

终于，找到了强力解决方法！

修复方法，打开my.cnf文件，把开启mysql的innodb强制修复属性

```
[mysqld]

innodb_force_recovery = 1
```

启动服务后，瞬间好了，数据完好无损，没有丢失。