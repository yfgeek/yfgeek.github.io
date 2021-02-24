---
title: Git 文件、文件夹区分大小写
date: 2017-01-07 02:25:32
tags: Github
categories: Github
header-img: "/content/images/githubauto/background.jpg"
---

其实非常简单，只是提醒一个小常识，Git默认是不区分文件，文件夹大小写的。

这会给很多人带来很多困扰，比如文件改了名，在严格区分大小写的系统下，实际上是两个不同名字的文件，但是Git不区分大小写机制导致无法正常的commit。

开启Git区分大小写

```
git config core.ignorecase false
```

最近爱上了给别人找bug，Pull Request，❤️Github。

任何细节，都很重要。

