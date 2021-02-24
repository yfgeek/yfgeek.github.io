---
title: 利用Dokku搭建自己的PaaS云服务器
date: 2018-10-10 21:54:16
tags: PaaS
categories: PaaS
banner_img: /banners/dokku.png
---

自从几年前用过各种云平台的部署方式（App Engine，云应用引擎），就感觉再也回不去了。每次用到传统的部署方式，都感觉这是**上个世纪**的感觉，可是痛点就在于，不管是阿里云、百度云、腾讯云，各种云平台的云应用引擎，早就开始逐渐的由免费转向收费，虽然价格不高，但是总感觉受制于人， 而且不同平台不同标准，各种平台的云服务让你越来越上瘾。

很早听[@WildCat](https://blog.daohanchong.com/#blog)大佬说过，可以自己搭建自己的PaaS平台（支持Java、Python、Ruby、PHP、Node、Go等），由于之前手上的服务器空间有限，一直没有试过，利用闲暇时间，买了台新的服务器，动手尝试了一下，果然不错。

![](https://avatars1.githubusercontent.com/u/13455795?s=400&v=4)

先简单说一下App Engine，各种App Engine最大的优点就是部署方便成本低，做为开发者，不需要管运行环境如何，只需要写好相关的配置文件，用git的方式，将代码上传到服务器上，服务器将代码放在docker容器内运行，服务器实现自动化部署。

至于配置安装过程很简单，按照[官网英文文档](http://dokku.viewdocs.io/dokku/getting-started/installation/)，一步一步来，问题不大，下面描述一下过程。

# 环境

官网中要求，要安装dokku，需要：

* 全新安装的Ubuntu 16.04 x64, Ubuntu 14.04 x64, Debian 8.2 x64 or CentOS 7 x64 (不建议)
* 至少1GB内存 （如果不足，官网也给出了解决方法）

之前尝试使用过Ubuntu18版本安装，安装未遂，后来老老实实的走官网的版本要求，一次性成功。

我是Centos的死粉，但是官网中声明，如果使用centos系统，将无法使用网页配置（可以使用cli进行配置）。

我的安装环境：Ubuntu 16.04 x64，内存1GB。

# 安装

```bash
wget https://raw.githubusercontent.com/dokku/dokku/v0.12.13/bootstrap.sh;
sudo DOKKU_TAG=v0.12.13 bash bootstrap.sh
```

在服务器上执行上述两句，服务器会用bash脚本自动安装，速度有点慢，如果没有任何报错，就操作完成了。

# 设置主域名&子域名

dokku支持子域名解析，这很帅气，我们可以用泛解析的方式，将IP都指向一个域名下。

举个例子，如果有个web 1，命名为web1，我们可以在git push程序后，自动部署并解析到你的域名下

`web1.xxx.com`，这种思路很适合做小的prototype或者demo。当然，如果你想设置单独的域名，我们可以使用`dokku domains`命令进行设置，[参考](http://dokku.viewdocs.io/dokku~v0.7.2/configuration/domains/)。

## 设置DNS解析

进入你的域名DNS管理网站，如阿里云、腾讯云、dnspod、cloudflare。本文以cloudflare为例。

添加A记录，Name 填写 *.lab IP 地址填写为你的服务器地址。

添加A记录，Name 填写 lab IP 地址填写为你的服务器地址。

## 进入Dokku Web配置页面设置

浏览器打开服务器IP地址，端口80，第一次配置会进入配置页面。

Public Key填写为你要上传git的时提交的公钥，位置`~\.ssh\id_rsa.pub`，如果没有，就用`ssh-keygen`生成一个并且放到`~\.ssh`目录下即可。

在配置页面中Hostname填写为服务器主地址，我的填写为 `lab.yfgeek.com`

同时勾选`Use virtualhost naming for apps`，允许使用子域名。

点击完成安装，就完成配置了。

## 部署前准备

## 语言支持

实际上，dokku是heroku的最小化版本，他们之间的例子是互通的，因此可以访问：

https://github.com/heroku

官方给了很多例子，搜搜demo或者对应语言，如java，找到相应的例子

node: [node-js-sample](https://github.com/heroku/node-js-sample)

ruby:[ruby-sample](https://github.com/heroku/ruby-sample)

rails:[ruby-sample](https://github.com/heroku/ruby-sample)

java:[ruby-rails-sample](https://github.com/heroku/ruby-rails-sample)

python:[java-sample](https://github.com/heroku/java-sample)

spring:[java-spring-sample](https://github.com/heroku/java-spring-sample)

django:[python-django-sample](https://github.com/heroku/python-django-sample)

scala:[scala-sample](https://github.com/heroku/scala-sample)

php:[php-getting-started](https://github.com/heroku/php-getting-started)

php:[facebook-template-php](https://github.com/heroku/facebook-template-php)

go-kafaka:[heroku-kafka-demo-go](https://github.com/heroku/heroku-kafka-demo-go)

此外还有很多

## 数据库支持

根据[官网](http://dokku.viewdocs.io/dokku/community/plugins/#official-plugins-beta)写的支持的数据库，可以有以下列表，数据库以插件的形式安装。

## 关系型数据库

| Plugin                                                       | Author                                      | Compatibility |
| ------------------------------------------------------------ | ------------------------------------------- | ------------- |
| [MariaDB](https://github.com/Kloadut/dokku-md-plugin)        | [Kloadut](https://github.com/Kloadut)       | 0.3.x         |
| [MariaDB (single container)](https://github.com/ohardy/dokku-mariadb) | [ohardy](https://github.com/ohardy)         | 0.3.x         |
| [MariaDB (single container)](https://github.com/krisrang/dokku-mariadb) | [krisrang](https://github.com/krisrang)     | 0.3.26+       |
| [PostgreSQL](https://github.com/jlachowski/dokku-pg-plugin)  | [jlachowski](https://github.com/jlachowski) | 0.3.x         |
| [PostgreSQL (single container)](https://github.com/ohardy/dokku-psql) | [ohardy](https://github.com/ohardy)         | 0.3.x         |
| [PostgreSQL (single container)](https://github.com/Flink/dokku-psql-single-container) | [Flink](https://github.com/Flink)           | 0.3.26+       |

## 缓存

| Plugin                                                       | Author                                  | Compatibility                                                |
| ------------------------------------------------------------ | --------------------------------------- | ------------------------------------------------------------ |
| [Nginx Cache](https://github.com/Aluxian/dokku-nginx-cache)  | [Aluxian](https://github.com/Aluxian)   | 0.5.0+                                                       |
| [Redis (single container)](https://github.com/ohardy/dokku-redis) | [ohardy](https://github.com/ohardy)     | 0.3.x                                                        |
| [Varnish](https://github.com/Zenedith/dokku-varnish-plugin)  | [Zenedith](https://github.com/Zenedith) | Varnish cache between nginx and application with base configuration |

## 队列

| Plugin                                                       | Author                                      | Compatibility |
| ------------------------------------------------------------ | ------------------------------------------- | ------------- |
| [RabbitMQ](https://github.com/jlachowski/dokku-rabbitmq-plugin) | [jlachowski](https://github.com/jlachowski) | 0.3.x         |
| [RabbitMQ (single container)](https://github.com/jlachowski/dokku-rabbitmq-single-plugin) | [jlachowski](https://github.com/jlachowski) | 0.3.x         |
| [ElasticMQ (SQS compatible)](https://github.com/cu12/dokku-elasticmq) | [cu12](https://github.com/cu12)             | 0.5.0+        |
| [VerneMQ (MQTT Broker)](https://github.com/SpinifexGroup/dokku-vernemq) | [mrname](https://github.com/mrname)         | 0.4.0+        |

## 其他

| Plugin                                                       | Author                                  | Compatibility |
| ------------------------------------------------------------ | --------------------------------------- | ------------- |
| [etcd](https://github.com/basgys/dokku-etcd)                 | [basgys](https://github.com/basgys)     | 0.4.x         |
| [FakeSNS](https://github.com/cu12/dokku-fake_sns)            | [cu12](https://github.com/cu12)         | 0.5.0+        |
| [InfluxDB](https://github.com/basgys/dokku-influxdb)         | [basgys](https://github.com/basgys)     | 0.4.x         |
| [RethinkDB](https://github.com/stuartpb/dokku-rethinkdb-plugin) | [stuartpb](https://github.com/stuartpb) | 0.3.x         |



# 部署第一个测试应用 

## 创建APP

登录服务器，执行dokku apps:create [name]创建应用

```bash
dokku apps:create ruby-rails-sample
```

## 安装数据库插件

本文以安装PostgreSQL为例

```bash
sudo dokku plugin:install https://github.com/dokku/dokku-postgres.git
```

## 创建应用的数据库

```
dokku postgres:create rails-database
```

## 关联数据库与APP

通过在代码后端设置读取`POSTGRES_URL`环境变量，连接数据库。

与此同时，我们需要将APP与数据库关联。

```
dokku postgres:link rails-database ruby-rails-sample
```

## 部署App

下载Sample代码，后将代码传到服务器上

```
git clone git@github.com:heroku/ruby-rails-sample.git
git remote add dokku dokku@lab.yfgeek.com:ruby-rails-sample
git push dokku master
```

值得一提的是，用户名必须是dokku，否则会出现问题。

```bash
Counting objects: 236, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (135/135), done.
Writing objects: 100% (236/236), 41.85 KiB | 10.46 MiB/s, done.
Total 236 (delta 85), reused 236 (delta 85)
-----> Cleaning up...
-----> Building ruby-rails-sample from herokuish...
-----> Setting config vars
       CURL_CONNECT_TIMEOUT:  90
-----> Setting config vars
       CURL_TIMEOUT:  600
-----> Adding BUILD_ENV to build environment...
-----> Ruby app detected
-----> Compiling Ruby/Rails
-----> Using Ruby version: ruby-2.2.1
-----> Installing dependencies using bundler 1.15.2
       Running: bundle install --without development:test --path vendor/bundle --binstubs vendor/bundle/bin -j4 --deployment
       Fetching gem metadata from https://rubygems.org/..........
       Fetching version metadata from https://rubygems.org/..
       Fetching dependency metadata from https://rubygems.org/.
       Using rake 10.4.2
       Fetching i18n 0.7.0
       Fetching json 1.8.2
       Fetching minitest 5.5.1
       Installing i18n 0.7.0
       Installing minitest 5.5.1
       Installing json 1.8.2 with native extensions
       Fetching thread_safe 0.3.5
       Installing thread_safe 0.3.5
       Fetching builder 3.2.2
       Installing builder 3.2.2
       Fetching erubis 2.7.0
       Fetching mini_portile 0.6.2
       Installing mini_portile 0.6.2
       Fetching rack 1.6.0
       Installing erubis 2.7.0
       Fetching mime-types 2.4.3
       Installing mime-types 2.4.3
       Fetching arel 6.0.0
       Installing rack 1.6.0
       Installing arel 6.0.0
       Using bundler 1.15.2
       Fetching coffee-script-source 1.9.1
       Fetching execjs 2.5.2
       Installing execjs 2.5.2
       Installing coffee-script-source 1.9.1
       Fetching thor 0.19.1
       Fetching hike 1.2.3
       Installing thor 0.19.1
       Installing hike 1.2.3
       Fetching multi_json 1.11.0
       Installing multi_json 1.11.0
       Fetching pg 0.18.1
       Fetching tilt 1.4.1
       Installing tilt 1.4.1
       Fetching rails_serve_static_assets 0.0.4
       Installing pg 0.18.1 with native extensions
       Installing rails_serve_static_assets 0.0.4
       Fetching rails_stdout_logging 0.0.3
       Installing rails_stdout_logging 0.0.3
       Using rdoc 4.2.0
       Fetching sass 3.4.13
       Installing sass 3.4.13
       Fetching tzinfo 1.2.2
       Installing tzinfo 1.2.2
       Fetching nokogiri 1.6.6.2
       Installing nokogiri 1.6.6.2 with native extensions
       Fetching mail 2.6.3
       Installing mail 2.6.3
       Fetching rack-test 0.6.3
       Installing rack-test 0.6.3
       Fetching puma 2.11.1
       Installing puma 2.11.1 with native extensions
       Fetching coffee-script 2.4.1
       Installing coffee-script 2.4.1
       Fetching sprockets 2.12.3
       Installing sprockets 2.12.3
       Fetching rails_12factor 0.0.3
       Installing rails_12factor 0.0.3
       Fetching loofah 2.0.1
       Installing loofah 2.0.1
       Fetching rails-html-sanitizer 1.0.2
       Installing rails-html-sanitizer 1.0.2
       Fetching activesupport 4.2.1
       Fetching sdoc 0.4.1
       Fetching uglifier 2.7.1
       Installing uglifier 2.7.1
       Installing sdoc 0.4.1
       Installing activesupport 4.2.1
       Fetching rails-deprecated_sanitizer 1.0.3
       Fetching globalid 0.3.5
       Fetching activemodel 4.2.1
       Installing rails-deprecated_sanitizer 1.0.3
       Installing activemodel 4.2.1
       Fetching jbuilder 2.2.12
       Fetching rails-dom-testing 1.0.6
       Installing globalid 0.3.5
       Fetching activerecord 4.2.1
       Installing jbuilder 2.2.12
       Installing rails-dom-testing 1.0.6
       Fetching activejob 4.2.1
       Fetching actionview 4.2.1
       Installing activejob 4.2.1
       Installing actionview 4.2.1
       Fetching actionpack 4.2.1
       Installing activerecord 4.2.1
       Installing actionpack 4.2.1
       Fetching actionmailer 4.2.1
       Fetching railties 4.2.1
       Fetching sprockets-rails 2.2.4
       Installing actionmailer 4.2.1
       Installing sprockets-rails 2.2.4
       Installing railties 4.2.1
       Fetching coffee-rails 4.1.0
       Fetching jquery-rails 4.0.3
       Fetching rails 4.2.1
       Installing coffee-rails 4.1.0
       Fetching sass-rails 5.0.3
       Installing jquery-rails 4.0.3
       Fetching turbolinks 2.5.3
       Installing sass-rails 5.0.3
       Installing turbolinks 2.5.3
       Installing rails 4.2.1
       Bundle complete! 13 Gemfile dependencies, 53 gems now installed.
       Gems in the groups development and test were not installed.
       Bundled gems are installed into ./vendor/bundle.
       Bundle completed (47.15s)
       Cleaning up the bundler cache.
       The latest bundler is 1.17.0.pre.1, but you are currently running 1.15.2.
       To update, run `gem install bundler --pre`
-----> Installing node-v8.10.0-linux-x64
-----> Detecting rake tasks
-----> Preparing app for Rails asset pipeline
       Running: rake assets:precompile
       I, [2018-10-10T05:36:01.119423 #1214]  INFO -- : Writing /tmp/build/public/assets/application-47fe71a3b34a93e1929c175b1755d405.js
       I, [2018-10-10T05:36:01.144742 #1214]  INFO -- : Writing /tmp/build/public/assets/application-c06dd6a542ea15147e6794e03643f87c.css
       Asset precompilation completed (7.21s)
       Cleaning assets
       Running: rake assets:clean
-----> Detecting rails configuration
-----> Discovering process types
       Procfile declares types -> web
-----> Releasing ruby-rails-sample (dokku/ruby-rails-sample:latest)...
-----> Deploying ruby-rails-sample (dokku/ruby-rails-sample:latest)...
-----> Attempting to run scripts.dokku.predeploy from app.json (if defined)
-----> App Procfile file found (/home/dokku/ruby-rails-sample/DOKKU_PROCFILE)
-----> DOKKU_SCALE file not found in app image. Generating one based on Procfile...
-----> New DOKKU_SCALE file generated
=====> web=1
-----> Attempting pre-flight checks
       For more efficient zero downtime deployments, create a file CHECKS.
       See http://dokku.viewdocs.io/dokku/deployment/zero-downtime-deploys/ for examples
       CHECKS file not found in container: Running simple container check...
-----> Waiting for 10 seconds ...
-----> Default container check successful!
-----> Running post-deploy
-----> Creating new /home/dokku/ruby-rails-sample/VHOST...
-----> Setting config vars
       DOKKU_PROXY_PORT:  80
-----> Setting config vars
       DOKKU_PROXY_PORT_MAP:  http:80:5000
-----> Configuring ruby-rails-sample.lab.yfgeek.com...(using built-in template)
-----> Creating http nginx.conf
-----> Running nginx-pre-reload
       Reloading nginx
-----> Setting config vars
       DOKKU_APP_RESTORE:  1
=====> Renaming container (e38662846bcf) cranky_colden to ruby-rails-sample.web.1
-----> Attempting to run scripts.dokku.postdeploy from app.json (if defined)
=====> Application deployed:
       http://ruby-rails-sample.lab.yfgeek.com
```

如果代码有更新，只需要重新push，应用就会自动重新部署，一步到位。

## 结语

既然回不去了，就不要回去了，使用Dokku这样的PaaS的部署方式，简单快捷，仿若又回到了几年前。

Dokku美中不足的就是，没有Web界面，所有操作都需要用Cli来实现，这样的门槛，会让很多小白望而却步。很多云服务厂商都提供这样的方式，但是也会提供界面，不过没有web服务带来的好处就是，能最大限度的节省云服务器的资源，让低内存低配置的电脑也能勉强跑起来。

以此类推，我们可以用这样的方法自动化部署更多的APP，同时也支持很多关系型、非关系型数据库，如Python、PHP、Java，轻松Git命令，后面的运维相关操作，根本不用你管，docker的方式，让我们更加便利的部署各种应用，更加安全，更加便捷可靠。

本文demo地址：http://ruby-rails-sample.lab.yfgeek.com

最后值得一提的是，官网的文档十分简明，提供了各种配置，本文介绍的比较具有局限性，建议到官网文档仔细拜读。

![](/content/images/dokku/1.png)

http://dokku.viewdocs.io/dokku~v0.7.2/getting-started/installation/



