---
title: OTR(Off-the-Record) 技术探讨
date: 2016-12-06 20:25:59
tags: Hack
categories: Hack
---

中文网络上对OTR(Off-the-record)技术文章甚少，实际上聊天上采用OTR技术具有极大的安全性。OTR是一种结合Diffie-Hellman密钥交换算法、AES算法、SHA-1哈希算法的加密在明文情况下传输的技术。

<!--more-->

通过OTR技术，能够实现:
- 加密聊天内容
- 证明与你聊天的人就是与你聊天的人（防止中间人攻击）
- 服务器无法记录你的真实聊天内容

# 原理
## 消息鉴别码(MAC)

在密码学中，消息鉴别码(Message Authentication Code)是经过特定算法后产生的一小段信息，检查某段消息的完整性，以及作身份验证。

![](/content/images/otr/1.png)

假设有两个聊天用户，一个叫Alice，一个叫Bob。

![](/content/images/otr/2.jpg)

例如：Alice要给一个叫Bob发信，Alice将明文MK使用Hash算法进行摘要提取，提取结果为Hash(MK)，之后用Alice的私钥对摘要进行签名SA[Hash(MK)]，之后将MK和SA[Hash(MK)]一同发给Bob。

其中SA[Hash(MK)]便可称之为消息鉴别码（MAC）。

它可以用来检查在消息传递过程中，其内容是否被更改过，不管更改的原因是来自意外或是蓄意攻击。同时可以作为消息来源的身份验证，确认消息的来源。

## 过程

第一步，使用Diffie-Hellman双方交换公钥，确认公钥可以信任（只大概叙述，取mod运算被简化）。

- Alice（用A表示）和Bob（用B表示）每人分别取一个数x,y,并且取一个g
- A->B: Alice 生成g^x，并且签名SignAlice(g^x)
- B->A: Bob 生成g^y，并且签名SignBob(g^y)
- SS=g^xy: 生成共享的公钥

第二步，使用公钥加密消息，消息传递。

- 计算EK=Hash(SS), MK=Hash(EK) 
- A->B: Encek(M), MAC(Encek(M),MK)
- Enc采用对称加密算法，比如AES算法用EK作为密钥
- Bob 用MK验证MAC, 用EK解密M
- 保密性和真实性得到了保证

第三步，更新密钥

- Alice and Bob 再取一个x',y'
- A->B: g^x’, MAC(g^x’, MK)
- B->A: g^y’, MAC(g^y’, MK)
- SS’ = H(g^x’y’)
- EK’ = H(SS’), MK’=H(EK’)
- 随后，Alice和Bob安全清除掉之前的SS, x, y, EK

第四步，继续保留 MK

- Alice 和 Bob 并不需要去清除MK
- 事实上，在他们发下一条消息的时候还用着之前的MK
- 这让中间人攻击者只能伪造过去的消息，并不能对现在的消息造成影响

从宏观的角度上来看，假设是Alice是消息发送方 发给 Bob是消息接受方，大致步骤可以分为：

1. Alice 和 Bob 都自行生成一对公私钥对，并将自己的公钥发送给对方
2. 若消息Alice打算发送消息M，则发送方首先用Bob的公钥和自己的私钥对M进行加密、签名，得到M1
3. M1经由服务器被发送给消息接收方
4. Bob用自己的私钥和Alice的公钥对M1进行解密、签名校验，最终还原出M


# Awesome OTRs
官网: https://otr.cypherpunks.ca/

pidgin-otr: https://otr.cypherpunks.ca/gentoo-install/otr-setup.html

JavaScript 实现: https://github.com/arlolra/otr

otr.to源代码(JS): https://github.com/OTRMan/otr.to-chat

一个python的otr项目: https://github.com/SpiralP/HexChat-otr

otrtalk(JS): https://github.com/mnaamani/otrtalk

Facebook的OTR实现Chrome插件:https://github.com/osnr/otron

一个基于OTR技术的P2P聊天系统: https://github.com/mehrvarz/P2pChatOTR

# 小结

研究算法的过程很有趣，也很累，每次应用OTR技术聊天的时候，我都强烈的感受到我的数据包被加密解密的过程，不知道为什么，感觉，异常，舒爽...
