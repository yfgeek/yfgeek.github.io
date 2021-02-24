---
title: Wat 吐槽JavaScript
date: 2016-12-21 01:17:26
tags:
 - 笑话
 - Web
categories: 笑话
header-img: "/content/images/wat/home-bg-o.jpg"

---

今天晚上在研究React技术栈头疼的时候，突然看到一个吐槽JavaScript的视频，简直搞笑，不过也挺客观的，毕竟是一个10天内攒出来的语言，虽然现在发展有ES支撑，但是依然非常零散，不过我按照视频里的做法试了一下，似乎有的返回结果不太一样。

# Wat

[![](https://www.destroyallsoftware.com/images/posters/talks/wat.poster.png)](https://www.destroyallsoftware.com/talks/wat)


```javascript
[] + []
""
[] + {}
"[object Object]"
{} + []
0
{} + {}
"[object Object][object Object]"
Array(16)
[undefined × 16]
Array(16).join("wat")
"watwatwatwatwatwatwatwatwatwatwatwatwatwatwat"
Array(16).join("wat" - 1 ) + " Batman!"
"NaNNaNNaNNaNNaNNaNNaNNaNNaNNaNNaNNaNNaNNaNNaN Batman!"
```
总之，看完这个视频，我就想说一句：

![](/content/images/wat/1.jpg)


# Javascript的出生与死亡

此外还有另一个视频 对JS从1995-2035年的历史阐述及大胆预测。



[![](https://www.destroyallsoftware.com/images/posters/talks/the-birth-and-death-of-javascript.poster.png)](https://www.destroyallsoftware.com/talks/the-birth-and-death-of-javascript)


十分客观的罗列出了JS的缺点、当前JS发展趋势必然产生的低效率问题。

![](/content/images/wat/2.png)
