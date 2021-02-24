---
title: Google镜像站的搭建
date: 2016-12-11 19:21:59
tags: Ops
categories: Ops
---

前两天，Google开发者大会高调的宣布Google Developers 网站终于正式上线了，希望Google能够解决一切问题，早日回归大陆市场。

<!-- more -->

# 教程
## 环境

Centos 6 

## 依赖
1. nginx
2. pcre 正则
3. ngx_http_proxy_module 反向代理
4. ngx_http_substitutions_filter_module 多重替换

## 安装
```bash
#
# 安装 gcc & git
#
apt-get install build-essential git gcc g++ make

#
# 下载最新版源码
# nginx 官网: 
# http://nginx.org/en/download.html
#
wget "http://nginx.org/download/nginx-1.7.8.tar.gz"

#
# 下载最新版 pcre
# pcre 官网:
# http://www.pcre.org/
#
wget "ftp://ftp.csx.cam.ac.uk/pub/software/programming/pcre/pcre-8.38.tar.gz"

#
# 下载最新版 openssl
# opessl 官网:
# https://www.openssl.org/
#
wget "https://www.openssl.org/source/openssl-1.0.1j.tar.gz"

#
# 下载最新版 zlib
# zlib 官网:
# http://www.zlib.net/
#
wget "http://zlib.net/zlib-1.2.8.tar.gz"

#
# 下载本扩展
#
git clone https://github.com/cuber/ngx_http_google_filter_module

#
# 下载 substitutions 扩展
#
git clone https://github.com/yaoweibin/ngx_http_substitutions_filter_module


#
# 解压缩
#
tar xzvf nginx-1.7.8.tar.gz
tar xzvf pcre-8.38.tar.gz
tar xzvf openssl-1.0.1j.tar.gz
tar xzvf zlib-1.2.8.tar.gz

#
# 进入 nginx 源码目录
#
cd nginx-1.7.8

#
# 设置编译选项
#
./configure \
  --prefix=/opt/nginx-1.7.8 \
  --with-pcre=../pcre-8.38 \
  --with-openssl=../openssl-1.0.1j \
  --with-zlib=../zlib-1.2.8 \
  --with-http_ssl_module \
  --add-module=../ngx_http_google_filter_module \
  --add-module=../ngx_http_substitutions_filter_module

#
# 编译, 安装
# 如果扩展有报错, 请发 issue 到
# https://github.com/cuber/ngx_http_google_filter_module/issues
#
make
sudo make install

#
# 启动, 安装过程到此结束
#
sudo /opt/nginx-1.7.8/sbin/nginx

vi /opt/nginx-1.7.8/conf/nginx.conf

```
http配置方式
```javascript
server {
  server_name <你的域名>;
  listen 80;

  resolver 8.8.8.8;
  location / {
    google on;
  }
}
```
https配置方式
```javascript
server {
  server_name <你的域名>;
  listen 443;

  ssl on;
  ssl_certificate <你的证书>;
  ssl_certificate_key <你的私钥>;

  resolver 8.8.8.8;
  location / {
    google on;
  }
}
```
随后
```bash
#
# 配置修改后, 需要 reload nginx 来让配置生效, 
#
sudo /opt/nginx-1.7.8/sbin/nginx -s reload
```

# 结果

测试一下网站，是可以访问的，出于一些原因，我个人搭建的镜像站不予公开。

# 参考技术

https://github.com/cuber/ngx_http_google_filter_module