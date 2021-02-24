---
title: 当我关注3万Github用户后发生了什么
date: 2016-11-19 11:36:26
tags: Github
categories: Github
header-img: "/content/images/githubauto/background.jpg"

---
# 前言
16天前，写了一个小python程序，自动关注Github里的人，python真是尤物，能快速麻出想做的程序。

于是乎我利用这个关注了3万人的Github，可是关注后~~除了同性交友~~似乎没有什么用，而我发现一件事，就是我的首页动态变化如同刷新浪微博一样，每个人都在star或fork各种项目，不乏很多精品资源。
<!-- more --> 
![](/content/images/githubauto/1.jpg)

翻都翻不过来，那么同时还存在一些“无用( ~~垃圾~~ )项目”，star和fork数量很少，点开没有意义。那么能否写一个python小程序，把“高质量”的项目来源于好友follow的项目都爬出来呢？

经过短暂的编码，``GithubAwesome.py``诞生了。

现在进入[AutoGithub](https://github.com/yfgeek/AutoGithub)项目就可以看到GithubAwesome.py项目

# 规则

设置一个规则，如果关注的人的动态里刷出来的项目 star数量 大于 最少 star  或 大于 最少 fork，那么就把他输出出来。

举个🌰
```
最少 star 数: 50
最少 fork 数: 50
```

效果如图

![](/content/images/githubauto/awesomesnap.png)

# 配置

首先，确保你安装了``beautifulsoup4``.

```
pip install beautifulsoup4
```
然后编辑``GithubAwesome.py``文件，请原谅如此土的方式。
```python
ga = GithubAwesome('username','password')
# login
ga.login()
ga.get_awesome(1,20,50) # 第一页 规则 min star 20 min or fork 20
```
# 写在最后

当你关注超过1K的用户，你会感叹，每天都有精品项目推送的感觉，真好！😁