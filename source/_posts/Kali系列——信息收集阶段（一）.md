---
title: Kali系列——信息收集阶段（一）
tags: 'Hack'
id: 6
updated: '2016-08-05 21:13:28'
date: 2016-07-30 17:48:20
---

记得前几周去参加过一个阿里巴巴举办的安全峰会，其中一个大牛讲到以前的渗透测试三字经，当场真是笑出声：
>进谷歌 找注入 没注入 就旁注 没旁注 用0day 没0day 猜目录 没目录 就嗅探

>爆账户 找后台 传小马 放大马 拿权限 挂页面  放暗连 清数据 拿不下 去自杀 死不了 继续来 查资料 去社工！

其实，我觉得渗透测试无论怎么变，不外乎这几点：

>信息收集阶段->漏洞检测阶段->漏洞利用阶段->持久化后门阶段

最近借了本好书《高度安全环境下的高级渗透测试》，这本书内容浅显易懂，但却很实用，虽然很多内容都知道，但是不成系统，这本书总结工作做得非常好，总结一下读书笔记。
<!-- more --> 
# 信息收集阶段
## DNS信息
其实DNS信息收集阶段用两个Linux系统自带工具即可完成。

### nslookup

此命令，可以快速查处ip地址
```bash
 ✘ ivan@Ivan~ nslookup zhihu.com
Server:		114.114.114.114
Address:	114.114.114.114#53

Non-authoritative answer:
Name:	zhihu.com
Address: 54.223.189.245
```

当然也可以指定dns查询服务器：
```bash
ivan@Ivan ~ nslookup -type=ns zhihu.com 8.8.8.8
Server:		8.8.8.8
Address:	8.8.8.8#53

Non-authoritative answer:
zhihu.com	nameserver = ns3.dnsv4.com.
zhihu.com	nameserver = ns4.dnsv4.com.

Authoritative answers can be found from:
```
### dig
dig真是个好工具，甚至可以找出DNS域传送漏洞。下面举一个例子，也是乌云网曝光过的，由于篇幅问题，精简了很多内容：
```bash
ivan@Ivan  ~  dig @dns.csust.edu.cn csust.edu.cn axfr

; <<>> DiG 9.8.3-P1 <<>> @dns.csust.edu.cn csust.edu.cn axfr
; (1 server found)
;; global options: +cmd
csust.edu.cn.		86400	IN	SOA	dns.csust.edu.cn. root.localhost.csust.edu.cn. 20160725 28800 7200 604800 86400
csust.edu.cn.		86400	IN	NS	dns.csust.edu.cn.
csust.edu.cn.		86400	IN	NS	dns2.csust.edu.cn.
csust.edu.cn.		86400	IN	A	113.240.233.146
csust.edu.cn.		86400	IN	A	210.43.192.8
csust.edu.cn.		86400	IN	A	210.43.192.18
csust.edu.cn.		86400	IN	MX	5 mxbiz1.qq.com.
csust.edu.cn.		86400	IN	MX	10 mxbiz2.qq.com.
csust.edu.cn.		86400	IN	AAAA	2001:da8:d002::d22b:c008
3g.csust.edu.cn.	86400	IN	A	113.240.233.6
app.csust.edu.cn.	86400	IN	A	113.240.233.11
app7.csust.edu.cn.	86400	IN	A	222.240.152.227
app8.csust.edu.cn.	86400	IN	A	113.240.233.8
archives.csust.edu.cn.	86400	IN	A	222.240.152.232
bid.csust.edu.cn.	86400	IN	CNAME	jtcloud.csust.edu.cn.
bw.csust.edu.cn.	86400	IN	A	10.255.194.20
cas.csust.edu.cn.	86400	IN	A	210.43.198.1
cslgqkzx.csust.edu.cn.	86400	IN	A	210.43.188.80
cslgxbsk.csust.edu.cn.	86400	IN	CNAME	cslgqkzx.csust.edu.cn.
cslgxbzk.csust.edu.cn.	86400	IN	CNAME	cslgqkzx.csust.edu.cn.
cwcx.csust.edu.cn.	86400	IN	A	10.22.14.252
cxlt.csust.edu.cn.	86400	IN	A	222.240.152.203
dkxnsyzx.csust.edu.cn.	86400	IN	A	222.240.152.200
dlkjxb.csust.edu.cn.	86400	IN	CNAME	cslgqkzx.csust.edu.cn.
dns.csust.edu.cn.	86400	IN	A	210.43.192.8
csust.edu.cn.		86400	IN	SOA	dns.csust.edu.cn. root.localhost.csust.edu.cn. 20160725 28800 7200 604800 86400
;; Query time: 62 msec
;; SERVER: 210.43.192.8#53(210.43.192.8)
;; WHEN: Sat Jul 30 20:18:47 2016
;; XFR size: 112 records (messages 1, bytes 2369)
```
可以看到，各种信息，从A、cname到MX全部暴露无遗。
如果不存在该漏洞则会被服务器拒绝查询请求。

```bash
ivan@Ivan ~ dig @dns4.tju.edu.cn tju.edu.cn axfr
; <<>> DiG 9.8.3-P1 <<>> @dns4.tju.edu.cn tju.edu.cn axfr
; (1 server found)
;; global options: +cmd
; Transfer failed.
```

### whois
whois查询，不多解释了

```bash
ivan@Ivan ~ whois yfgeek.com

Whois Server Version 2.0

Domain names in the .com and .net domains can now be registered
with many different competing registrars. Go to http://www.internic.net
for detailed information.

   Domain Name: YFGEEK.COM
   Registrar: GODADDY.COM, LLC
   Sponsoring Registrar IANA ID: 146
   Whois Server: whois.godaddy.com
   Referral URL: http://www.godaddy.com
   Name Server: F1G1NS1.DNSPOD.NET
   Name Server: F1G1NS2.DNSPOD.NET
   Status: clientDeleteProhibited https://icann.org/epp#clientDeleteProhibited
   Status: clientRenewProhibited https://icann.org/epp#clientRenewProhibited
   Status: clientTransferProhibited https://icann.org/epp#clientTransferProhibited
   Status: clientUpdateProhibited https://icann.org/epp#clientUpdateProhibited
   Updated Date: 21-apr-2016
   Creation Date: 23-nov-2012
   Expiration Date: 23-nov-2017

```
