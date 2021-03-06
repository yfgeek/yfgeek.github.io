---
title: BlockVotes 一个基于区块链的环签名投票系统
date: 2017-08-10 01:10:41
tags: Blockchain
categories: Blockchain
header-img: "/content/images/blockvotes/background.jpg"
---

好久好久没有写Blog了，最近在忙我的论文，题目是一个基于区块链的环签名投票系统。

听着名字挺绕的，实际上也是挺绕的，我做这个题目实际上是将三个学术领域的东西放在了一起：

* 电子投票 (e-voting) 属于密码学
* 区块链(blockchain) 属于密码学的应用
* 环签名(ring-signature) 属于密码学

如果对以上三个概念无任何了解，基本上就告别理解我做的东西了。这个项目目前处于私有项目状态之中，由于存在毕业设计可能会代码查重的问题，在九月初，将会对本项目彻底开源。

# 理念

<img src=/content/images/blockvotes/logo.png width=300 style="margin: 0;">

## 电子投票？

实际上，这个学术命题属于电子投票方向的，在电子投票学术方向上属于创新型，在区块链上属于应用型，在谈及这些问题之前，得先说说学术上的电子投票的一些概念：

![](/content/images/blockvotes/security.png)

**隐私性:**

- 选票保密性 (BP): 任何攻击者都无法把用户所投的票和投票者的身份联系起来。
- 无收据性 (RF): 投票者在投票后不能证明他是怎么投票的。系统不允许投票者在投票后证明这些。
- 不可强制性 (CR): 任何攻击者都无法强制用户生成自己不想投的票据。 攻击者不会强迫选民得到相应的收据以供强迫者检查。

**可验证性:**

- 个体可验证性：每个投票者可以验证自己的投票是不是倍正确计入所有投票。
- 全局验证性：任何投票者都可以验证其他投票者所投票的合法性。

**不可重投性:**

不可重投性指的是，任何投票者无法对相同的消息进行两次投票。

**公平性:**

公平性指的是，任何人都无法提前知道选票的结果，以及无法影响选票的结果。

其实还有很多很多概念，具体可以参考引用中的第一个论文。

实际上，隐私性和可验证性在一定程度上是互相矛盾的，保障可验证性的情况下就可能无法保证隐私，我们在电子投票的方面的目标是尽可能的保障以上的目标。

所以，设计一个系统是从隐私性和可验证性的角度上出发的，这也就解释了，为什么用区块链，为什么用环签名。

## 为什么用区块链

区块链是什么？区块链可以理解为“分布式公共账本”，按照计算机的角度上，如果你对区块链一窍不通，**你可以理解为一个可 增加、查找 却不能修改、删除的 分布式实时数据库**。而比特币中的交易信息就是存在这个数据库之中的。

如果你还是不理解，请**千万不要到互联网上搜索什么是比特币、比特币挖矿原理、什么是区块链，只会让你更加疑惑**，他们试图把区块链描述的非常高大上，迷惑你，并且告诉外人，这是很高大上的东西，其实不是，如果你真正研究过区块链的协议，你会觉得其实不是那么回事。在此，对于那些故作玄虚的文章，我想引用《LaTeX新人教程，30分钟从完全陌生到基本入门》文章所说的一句话表达我的心情：

> 我要严厉警告和强烈声讨那些自以为是advanced LaTeX user的人。请你们不要为了自己那一点可怜可悲的虚荣心，去刻意渲染LaTeX有多么高端多么不容易学习，这和孔乙己炫耀茴香豆的茴字有四种写法有什么区别么？混账！LaTeX到底有多“难”你们自己清楚好么？

所以，我附了一篇论文[3]，不过是英文版的，但是都是纯白话口语讲述到底什么是区块链，比特币的挖矿原理，实际上是非常浅显易懂的。如果有时间，恰当的时候，我也可以翻译成中文。

---

世界上第一个区块链是比特币创造的，比特币需要区块链才能够叫做区块链，为什么？

因为作为“货币“，最根本的存在的意义是什么？

证明资产？自己是富二代？不不不，不是的。

根本意义实际上是交易，人类社会，有了交易，才有了货币，货币是一般等价物，这个我们在中学都学过。

那么作为一种货币，货币是要流通的，如果顾客买了商家的东西，是不是要顾客给商家付款呀？天经地义的。

这个过程就是交易，那么，如何证明顾客已经把钱付给了商家？

在现实生活中，太简单了，根本不用证明，顾客直接把钱给商家就行了。

但是，如果顾客用银行卡或者支付宝呢？

好！我们的概念来了，这个概念叫 账本。

什么是账本？实际上就是银行的这笔交易的流水，在你用银行卡划卡支付的过程中，实际上是顾客和商家都信任银行，认为银行记下来的这个账是正确无误的，并且银行也把顾客的钱减少了，把商家的钱增加了。那么这个记账的媒介，该笔账单，可以说是账本的一部分。

如果你的银行账户消费的内容足够多，那么这就是一个账本，上面记录着你的所有的交易信息，交易信息和你的账户余额的值一一对应，在这种情况下，你相信银行，你相信账本，银行作为第三方支付方式，你也就自然就信任了。

可是我为什么要提出账本的概念呢？

区块链就是一个分布式的公共记账账本。凡是下载了比特币(或XX币，我这里用比特币举例)的客户端的人，都会自动下载这个账本，这个账本储存着全球所有人的通过比特币交易信息。这个信息只能增加，不可以删除、修改，否则就无法解决同步的问题。

那么我们就可以理解区块链，就是一个分布式的不可删改的数据库，现在理解了吧？

回归正题，为什么要用区块链，因为区块链的广播机制是通过比特币进行交易而储存的，每个交易中都会有一个东西叫做附言(OP_RETURN)，在附言位置可以留自己的转账信息比如：

> 哈，这笔交易你收好！是小明转给你的

这个信息是随意的，可以写任何东西。

[真实的例子](https://www.blocktrail.com/tBTC/tx/cb83c02e94d41851d75cfaa31b5318250267cbc043e72740562fdaf8c612b0cf) 附言内容是 haha

那么聪明的人来了，这段信息是不是可以用来储存东西，虽然有容量限制，但是储存的东西就是永久的不会被更改的。

不错，我创建一个公共的计票的比特币的账户，并且公开地址，每个投票者对这个地址进行转账，并且附上自己的投票信息，比如候选人什么的作为附言，然后进行转账，内容就是相当于“发布到了区块链”上了。

> 区块链技术基于去中心化的对等网络，用开源软件把密码学原理、时序数据和共识机制相结合，来保障分布式数据库中各节点的连贯和持续，使信息能即时验证、可追溯、但难以篡改和无法屏蔽，从而创造了一套隐私、高效、安全的共享价值体系。

高判立下，这样每个投票者的投票信息就是公开的可以验证的，但是又由于比特币地址本身具有匿名性，谁也不知道发布投票信息的人是谁，这又符合了隐私性。

你看，是不是完美了？

## 为什么用环签名

不是，并不完美。

为什么？

那在这种情况下，岂不是任何人都可以投票了，不经过投票中心认证的人，都可以瞎投，扰乱视听，而且投票者无法验证整体性。

没错，这个时候伟大的环签名来了。

在理解环签名之前，你需要对密码学的签名机制有所了解，如果不了解，请[点击这里](http://www.ruanyifeng.com/blog/2011/08/what_is_a_digital_signature.html)。

环签名，实际上是一种特殊的群签名。你不用理解什么是群签名，只需要听我继续往下讲什么是环签名。

![](/content/images/blockvotes/ring1.png)

![](/content/images/blockvotes/ring2.png)

你看，我附了一个山东大学一个优秀的博士[论文](https://wenku.baidu.com/view/5e43126f25c52cc58bd6be40.html)截图，是不是瞬间就显得高大上？

其实也没有什么的。

环签名是对一个特定的内容进行签名。

在签名之前，所有人围城一个环，每个人生成一个自己的私钥Si，并且告诉其他人自己的公钥(P1,P2,P3...Pn)。

接下来就是开始签名，每个人用自己的私钥Si和所有人的公钥(P1,P2,P3...Pn)对内容m进行签名，可以得到签名s。

注意：

* 每个人的得到的签名都是不一样的
* 签名的长度取决于公钥的个数

这样，我们把m替换为我们的投票内容，这样就可以在组织投票之前，对能够投票的人进行圈定，只有在这个圈子里的人才能投票。

环签名具有天然的可验证性，任何人都可以验证自己，只需要根据得到的签名和所有人的公钥就可以得出自己是不是真正的投票了。

如果把签名信息s也随着信息m一起广播到区块链上，那是不是就能够保障整体验证性与个体验证性？

好了，我们投票的主要矛盾解决了！现在，真的解决了！

你可能一句都没看懂我上面在说什么，也可能看懂了一点，没事，摸摸头，是我表达能力太差了，其实都是非常简单的东西。

## 具体投票协议

我不告诉你哈哈哈哈哈哈，会在九月份公开。

都是一些数学表达，发了也得花大量篇幅去解释，费劲。

因为内容可能会查重，即使是中文版本现在我也不想发。

# 技术栈

![](/content/images/blockvotes/stack.png)

后端语言：PHP

后端数据库：MySQL

区块链：比特币BTC/比特币测试网络TESTNET

PHP MVC 框架：Slim

PHP DAO 框架：Illuminate Database

PHP 依赖管理： Composer

PHP 模板引擎：TWIG

前端包管理：NPM

前端CSS预处理器：Sass

前端代码打包工具：Gulp

前端预编译器：browserify

版本控制：Git

说句实话，技术挺复杂的，用到了很多第三方类库、框架、工具，感谢很多第三方类库的帮助，在我写代码的时候，也发现了一个第三方类库的Bug，并随手修复了，也算是对开源社区的回馈吧。

![](/content/images/blockvotes/fixedbug.png)

# 关于开源

项目会开源的，我知道，区块链最近创业很多，我的这个项目也可以用来创业，但是我根本不在乎这些金钱的东西，**我更享受开源所带来的快感**。

但是如果拿去用来做商业用途，我还是很不爽的，所以我的这个项目禁止用于商业用途，但是非营利用途都是可以的。

# 截图

可能不是最终版，先尝鲜一下

![](/content/images/blockvotes/1.png)

![](/content/images/blockvotes/2.png)

![](/content/images/blockvotes/3.png)

![](/content/images/blockvotes/4.png)

![](/content/images/blockvotes/5.png)

![](/content/images/blockvotes/6.png)

![](/content/images/blockvotes/7.png)

![](/content/images/blockvotes/8.png)

![](/content/images/blockvotes/9.png)

![](/content/images/blockvotes/10.png)



# 本文参考文献

[1] Jonker H, Mauw S, Pang J. Privacy and verifiability in voting systems: Methods, developments and trends[J]. Computer Science Review, 2013, 10:1-30.

[2] 张江霄, 李舟军, 刘霞,等. 基于群盲签名的多授权电子投票系统[J]. 中国科技论文, 2015(8):980-983.

[3] Mercer R. Privacy on the Blockchain: Unique Ring Signatures[J]. 2016.