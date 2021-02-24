---
title: 使用ProxyChains进行终端代理
date: 2016-09-26 00:46:04
tags: Hack
categories: Hack
---

# 安装
```
brew install proxychains-ng
```
# 配置
```
vi /usr/local/etc/proxychains.conf
```
# 使用
```
proxychains4 ssh xx:xx@xx.com
```