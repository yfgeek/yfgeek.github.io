---
title: Traefik 2 群晖硬核指南
date: 2021-03-10 18:16:30
tags: traefik
categories: docker
banner_img: /banners/traefik.png
---

家中群晖nas使用docker运行各种各样的应用也有几年了，多半是自用，同时软路由启动着DDNS自动解析域名的服务。前几天，想和朋友共享gitea，创建一个私有的git服务器，决定再次维护一次。

> **阶段一**：家中的各种docker容器均各自映射了各自的端口，通过路由器端口映射映射至互联网。
>
> 缺点：1. 端口混乱难以记住 2. 缺乏统一日志记录。
>
> **阶段二**：我又创建了一个docker内部网，在总入口启动了一个nginx容器，同时把各应用加入到docker内部网中，将应用的端口统一，通过nginx配置虚拟主机，路由到不同的应用下，后端地址依赖于容器的主机名解析。
>
> 缺点：1. 容器变动起来十分麻烦，无法轻松的增加容器副本的负载均衡；2. nginx重启强制依赖于各服务的运行状态，否则会启动失败；3. 繁琐的配置懒得手动申请SSL。
>
> **阶段三**：因路由器的配置问题，上行速度无法跑满，去掉了硬件路由器的方式，在群晖nas上启动了lede软路由，软路由作为DMZ区域，所有出口流量走软路由，速度极大的提升了，北京地区ms级别极速打开，上行速度跑满，但上述问题，依然繁琐。

我的VPS用过一段时间的caddy，可基于acme自动注册SSL、续签SSL。考虑到容器时代可能会自由启停增加容器副本，nginx增改配置文件的方式，在服务配置、域名配置、SSL配置方面，**显得传统而又繁琐**。

为了彻底改变这种落后的机制，刚好在研究k8s的时候，我发现了traefik，刚好能满足我的需求。然而网上大部分内容均为k8s相结合的ingress-traefik，虽然k8s确实香，但家用nas基本上和k8s扯不上关系也不需要，网上仅有一篇文章描述群晖如何安装traefik1，决定分享一下我容器化部署taefik2的填坑经验。

# Traefik

![](/content/images/traefik/traefik-architecture.png)

Traefik 是一个开源的可以使得服务发布变得轻松有趣的边缘路由器。它负责接收你系统的请求，然后使用合适的组件来对这些请求进行处理。

使用 Traefik，不需要维护或者同步一个独立的配置文件：因为一切都会自动配置，实时操作的（无需重新启动，不会中断连接）。使用 Traefik，你可以花更多的时间在系统的开发和新功能上面，而不是在配置和维护工作状态上面花费大量时间。

除了众多的功能之外，Traefik 的与众不同之处还在于它会自动发现适合你服务的配置。当 Traefik 在检查你的服务时，会找到服务的相关信息并找到合适的服务来满足对应的请求。

# Traefik 部署

前提：

> 1. 群晖打开了SSH
> 2. 安装了docker套件
> 3. 默认磁盘目录是volume1

SSH登录至群晖，执行如下命令：

```bash
cd /volume1/docker/
mkdir traefik
cd traefik
mkdir config
mkdir ssl
# 创建traefik专属网络
docker network create traefik
touch docker-compose.yml
```
亮出我们的利器，通过docker-compose启动，编辑刚刚创建的yml文件 `docker-compose.yml`：

```yml
version: '3.7'

services:

  traefik:
    container_name: traefik
    image: traefik:v2.1.3
    restart: always
    environment:
      - CF_API_EMAIL=CLOUDFLARE登录邮箱
      - CLOUDFLARE_DNS_API_TOKEN=CLOUDFLARE创建的DNS TOKEN
      - CLOUDFLARE_ZONE_API_TOKEN=CLOUDFLARE创建的API TOKEN
    ports:
      - "52080:80"
      - "52443:443"
      - "52022:22"
    networks:
      - traefik
    command: traefik --configFile /etc/traefik.toml
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro
      - ./ssl/:/data/ssl/:rw
      - ./traefik.toml:/etc/traefik.toml:ro
      - ./config/:/etc/traefik/config/:ro
    healthcheck:
      test: ["CMD-SHELL", "wget -q --spider --proxy off localhost:8080/ping || exit 1"]

networks:
  traefik:
    external: true
```

值得注意的是，因为运营商屏蔽了`22`、`443`、`80`端口，于是我使用`52022`、`52443`、`52080`做映射替代，如未被屏蔽，请忽略。

为了自动申请SSL证书，提交成功率，我才用DNS Challenge的方式校验有效性，因此需Cloudflare的环境信息，如果你的域名没有配置Cloudflare解析，可以根据[这篇文章](https://doc.traefik.io/traefik/master/https/acme/)置换相应变量。

![image-20210311225314293](/content/images/traefik/ddns.png)

同时，我们在`traefik`目录下创建`traefik.toml`文件，作为全局配置文件，一般不会有改动。

```ini
[global]
  checkNewVersion = false
  sendAnonymousUsage = false

[log]
  level = "WARN"
  format = "common"

[api]
  dashboard = true
  insecure = true

[ping]

[accessLog]

[providers]
  [providers.docker]
    watch = true
    exposedByDefault = false
    endpoint = "unix:///var/run/docker.sock"
    swarmMode = false
    useBindPortIP = false
    network = "traefik"
  [providers.file]
    watch = true
    directory = "/etc/traefik/config"
    debugLogGeneratedTemplate = true

[entryPoints]
  [entryPoints.http]
    address = ":80"
  [entryPoints.https]
    address = ":443"
  [entryPoints.ssh]
    address = ":22"

[certificatesResolvers.my.acme]
  email = "CLOUDFLARE登录邮箱"
  storage = "/data/ssl/acme.json"
  [certificatesResolvers.my.acme.dnsChallenge]
    resolvers = ["1.1.1.1:53", "8.8.8.8:53"]
    provider = "cloudflare"
    delayBeforeCheck = 30
```

随后，进入`config`目录。

创建`dashboard.nas.yfgeek.com.toml`文件，作为traefik展示面板的配置文件，因为相对固化，所以采用文件配置文件的形式，traefik支持文件加载配置。

```ini
[http.middlewares.dash-compress.compress]
[http.middlewares.dash-auth.basicAuth]
  users = [
    "test:$apr1$ux2tmr06$Qz5r/BNEfzdxriR/0o30Z1",
  ]

[http.routers.dashboard]
  rule = "Host(`dashboard.nas.yfgeek.com`)"
  entrypoints = ["http"]
  service = "dashboard@internal"
  middlewares = ["dash-auth", "dash-compress"]

[http.routers.api]
  rule = "Host(`dashboard.nas.yfgeek.com`) && PathPrefix(`/api`)"
  entrypoints = ["http"]
  service = "api@internal"
  middlewares = ["dash-auth", "dash-compress"]

[http.routers.ping]
  rule = "Host(`dashboard.nas.yfgeek.com`) && PathPrefix(`/ping`)"
  entrypoints = ["http"]
  service = "ping@internal"
  middlewares = ["dash-auth", "dash-compress"]
```

这个文件中，我们设置了一个用于验证的中间件，进入网站之前会默认进行验证，密码是*htpasswd*生成规则，例子中用户名是test，密码是test，如需定制，可到[这个网站生成密码](https://hostingcanada.org/htpasswd-generator/)。

全部创建好后，目录结构如下：

> |____config
> | |____dashboard.nas.yfgeek.com.toml
> |____ssl
> |____docker-compose.yml
> |____traefik.toml

确认无误，我们使用启动大法，执行如下命令。

```bash
docker-compose up -d
docker container logs traefik -f
```

不出意外，服务即将启动成功，可以看到日志，如果有问题就见招拆招即可。

![image-20210311225314293](/content/images/traefik/dashboard.png)

# Gitea 部署

traefik启动好后，我们还没有应用接入，接下来，整理一下原有的gitea容器，将其写为docker compose文件，同理，在docker目录下新建一个gitea目录。

```bash
cd /volume1/docker/
mkdir gitea
cd gitea
touch docker-compose.yml
```

`docker-compose.yml `参考如下：

```yaml
version: '3'

services:

  gitea:
    container_name: gitea
    image: gitea/gitea:latest
    restart: always
    environment:
      - USER_UID=1000
      - USER_GID=1000
    networks:
      - traefik
    expose:
      - 3000
      - 22
    labels:
      - traefik.enable=true
      # 设置HTTP强制跳转HTTPS 中间件
      - traefik.http.middlewares.redirect-https.redirectscheme.scheme=https
      - traefik.http.middlewares.redirect-https.redirectscheme.port=52443

      # HTTP配置
      - traefik.http.routers.gitea-web.rule=Host(`git.nas.yfgeek.com`,`git.yfgeek.com`)
      - traefik.http.routers.gitea-web.entrypoints=http
      - traefik.http.routers.gitea-web.service=gitea-web
      - traefik.http.routers.gitea-web.middlewares=redirect-https

      # HTTPS配置
      - traefik.http.routers.gitea-web-secure.rule=Host(`git.nas.yfgeek.com`,`git.yfgeek.com`)
      - traefik.http.routers.gitea-web-secure.entrypoints=https
      - traefik.http.routers.gitea-web-secure.service=gitea-web
      - traefik.http.routers.gitea-web-secure.tls=true
      - traefik.http.routers.gitea-web-secure.tls.certresolver=my
      - traefik.http.services.gitea-web.loadbalancer.server.port=3000

      # SSH配置
      - traefik.tcp.routers.gitea-ssh.rule=HostSNI(`*`)
      - traefik.tcp.routers.gitea-ssh.entrypoints=ssh
      - traefik.tcp.routers.gitea-ssh.service=gitea-ssh
      - traefik.tcp.services.gitea-ssh.loadbalancer.server.port=22
    volumes:
      - /volume1/docker/gitea:/data


networks:
  traefik:
    external: true
```

traefik是通过增加`label`的方式，自动注册服务的，无需任何配置文件，实时生效，如果容器挂了，转发关系自动失效。配置文件中，利用`redirectscheme`的中间件方式，增加了HTTP强制跳转至HTTPS。

针对HTTPS的访问方式，traefik会自动注册访问域名``rule=Host(`git.nas.yfgeek.com`,`git.yfgeek.com`)``的两个域名的，因此一定要确认无误。

同时，为了实现后续的SSL自动签发、host自动注册，我在DDNS中增加了域名的泛解析配置。

确认无误后，执行如下命令

```bash
docker-compose up -d
```

这里有一个小坑，值得注意，acme申请证书需要一段时间，如果配置信息有误，是不会申请成功的。因此需要查看下traefik的日志。

```bash
docker container logs traefik -f
```

找到问题见招拆招，如果是权限等问题，exec进入容器，通过chmod命令解决即可。

随后访问域名的HTTP(http://git.yfgeek.com:52080)，将被强制跳转至HTTPS(https://git.yfgeek.com:52443)，即可看到SSL证书自动申请、配置成功。与此同时，基于TCP的SSH服务也被顺利转发。

![image-20210311225314293](/content/images/traefik/gitea.png)

反过来，我们查看dashboard，即可看到gitea的服务已经成功在dashboard中注册了，并且一切顺利。

![image-20210311225314293](/content/images/traefik/gitea2.png)

![image-20210311225314293](/content/images/traefik/gitea3.png)

类似的，如果后续增加新的docker应用，可直接编辑docker compose文件，增加label，配置域名，直接解析、自动申请SSL，方便极了，生产力得到了极大的提升。

下一期我将讲讲如何`traefik`+`ptomethus`+`granafa`，实现自动采集数据，归集日志，构建网站监控系统，欢迎持续关注。


参考文章

1. https://juejin.cn/post/6844904053722316813
2. https://soulteary.com/2020/02/01/configure-traefik-v2-based-web-server.html
3. https://www.qikqiak.com/traefik-book/
4. https://doc.traefik.io/traefik/master/https/acme/