---
title: GHOST搬迁到HEXO(Github Pages)指南
date: 2016-09-12 21:41:59
tags: Web
categories: Web
---
# 前言

想了很长时间，权衡了一下利弊，还是觉得Github Pages平台会好一些，虽然可能我用了几天就会被和谐掉。

但是GHOST还需要找专门能够运行Node的平台，在这方面，全静态的HEXO绝对是博客的首选。
# 前提
在搬家之前，首先你要有一个Github，或者Gitlab，本文不过多叙述：

* 安装HEXO
* 配置Github

# 搬家
## 第一步
Ghost导出数据json：实验室-导出，下载好json

## 第二步
安装搬家工具
```
npm i hexo-migrator-ghost --save  
```
## 第三步

修改`./scaffolds/post.md`文件内容为：

```
title: {{ title }}  
date: {{ date }}  
tags: {{ tags }}  
---
```
## 第四步
将json文件放到根目录，并且执行：

```
hexo migrate ghost source.json  
hexo clean ; hexo d -g 
```

# 注意
经过以上步骤，基本上是搬家完毕了，但是仍有一些需要注意的：
* HEXO的Markdown语法比较严谨，所以倒入进来会存在问题，需要进一步修正，比如#如果前面不空格是不解析标题的
* tag可能会出问题，会自动加--，请自行删除，或者批量删除