---
title: 揭秘utf8=✓背后的小秘密
date: 2017-02-17 16:51:18
tags: Web
categories: Web
header-img: "/content/images/f2e/post-bg-js-version.jpg"
---

你可能和我一样疑惑，为什么github等网站，在POST数据的时候，经常带有`utf8=✓`字符，显然这种方式看起来很难以接受，因为Unicode编码错误的话会导致很多问题，毕竟用户的浏览器是不一样的。

为什么不用`utf8=true`来代替呢？

答案恰恰相反！

`utf8=✓`就是为了让浏览器在POST数据时强制使用utf-8的编码方式，进而在后端代码中无需考虑字符串编码转换问题，以免带来困扰。

在IE8，或者更早的IE浏览器里，在POST数据时，会尽可能的POST以Latin-1编码方式的字符串，这给后端带来了巨大的兼容性的困扰，但是你一旦在POST数据中包含了✓这样的Unicode字符，就会自动用uft8的编码方式发送字符串。

是不是很有用的小技巧呢？

本文翻译自:[Is the use of “utf8=✓” preferable to “utf8=true”?](http://softwareengineering.stackexchange.com/questions/168751/is-the-use-of-utf8-preferable-to-utf8-true)