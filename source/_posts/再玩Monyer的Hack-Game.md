---
title: 再玩Monyer的Hack Game
date: 2016-11-23 19:15:20
tags: Hack 
categories: Hack
---

之前混百度空间混久了，认识很多大牛，不乏monyer大牛。很早很早以前，他做了一个黑客小游戏，当时还不太会玩，现在突然打开发现，网站竟然还活着，决定重温经典。

<!-- more -->

# 第零关

http://monyer.com/game/game1/

> 请点击链接进入第1关： 连接在左边→ ←连接在右边

可是我们看不到链接地址，直接打开审查工具。

```html
<span>连接在左边→</span>
<a href="first.php"></a>
<span>←连接在右边</span>
```
好的输入地址，我么进入第一关

# 第一关

http://monyer.com/game/game1/first.php

打开页面依然是什么都没有，这很正常。审查工具看代码。

```html
<script type="text/rocketscript" data-rocketoptimized="true">
    function check(){
        if(document.getElementById('txt').value=="  "){
            window.location.href="hello.php";
        }else{
            alert("密码错误");
        }
    }
</script>
```

好了 我们知道密码了，并且知道下一关的文件是hello.php

# 第二关

http://monyer.com/game/game1/hello.php

审查工具看源代码，head中

```html
<script type="text/rocketscript" data-rocketoptimized="true">
    document.oncontextmenu=function(){return false};

    var a,b,c,d,e,f,g;
    a = 3.14;
    b = a * 2;
    c = a + b;
    d = c / b + a;
    e = c - d * b + a;
    f = e + d /c -b * a;
    g = f * e - d + c * b + a;
    a = g * g;
    a = Math.floor(a);

    function check(){
        if(document.getElementById("txt").value==a){
            window.location.href=a + ".php";
        }else{
            alert("密码错误");
            return false;
        }
    }
</script>
```

现在是2016年了，我们不需要手算和计算器了。直接在chrome的console里输入
```javascript
var a,b,c,d,e,f,g;
    a = 3.14;
    b = a * 2;
    c = a + b;
    d = c / b + a;
    e = c - d * b + a;
    f = e + d /c -b * a;
    g = f * e - d + c * b + a;
    a = g * g;
    a = Math.floor(a);
    
424178
```
好的我们知道a的值是424178，输入，进入下一关

# 第三关

http://monyer.com/game/game1/424178.php

head中存在js

```javascript
<script type="text/rocketscript" data-rocketoptimized="true">
    eval(String.fromCharCode(102,117,110,99,116,105,111,110,32,99,104,101,99,107,40,41,123,13,10,09,118,97,114,32,97,32,61,32,39,100,52,103,39,59,13,10,09,105,102,40,100,111,99,117,109,101,110,116,46,103,101,116,69,108,101,109,101,110,116,66,121,73,100,40,39,116,120,116,39,41,46,118,97,108,117,101,61,61,97,41,123,13,10,09,09,119,105,110,100,111,119,46,108,111,99,97,116,105,111,110,46,104,114,101,102,61,97,43,34,46,112,104,112,34,59,13,10,09,125,101,108,115,101,123,13,10,09,09,97,108,101,114,116,40,34,23494,30721,38169,35823,34,41,59,13,10,09,125,13,10,125));
</script>
```

新时代无须解释，直接上chrome consle
```javascript
String.fromCharCode(102,117,110,99,116,105,111,110,32,99,104,101,99,107,40,41,123,13,10,09,118,97,114,32,97,32,61,32,39,100,52,103,39,59,13,10,09,105,102,40,100,111,99,117,109,101,110,116,46,103,101,116,69,108,101,109,101,110,116,66,121,73,100,40,39,116,120,116,39,41,46,118,97,108,117,101,61,61,97,41,123,13,10,09,09,119,105,110,100,111,119,46,108,111,99,97,116,105,111,110,46,104,114,101,102,61,97,43,34,46,112,104,112,34,59,13,10,09,125,101,108,115,101,123,13,10,09,09,97,108,101,114,116,40,34,23494,30721,38169,35823,34,41,59,13,10,09,125,13,10,125)
```
输出为
```javascript
function check(){
	var a = 'd4g';
	if(document.getElementById('txt').value==a){
		window.location.href=a+".php";
	}else{
		alert("密码错误");
	}
}
```
好了我们知道了，密码是d4g，下一关！

# 第四关

http://monyer.com/game/game1/d4g.php

这一关非常猥琐，页面会自动跳转，稍有不注意，就跳走了。
打开后立即按esc停止。

```javascript
<script type="text/rocketscript" data-rocketoptimized="true">
    eval(function(p,a,c,k,e,d){e=function(c){return c.toString(36)};if(!''.replace(/^/,String)){while(c--)d[c.toString(a)]=k[c]||c.toString(a);k=[function(e){return d[e]}];e=function(){return'\\w+'};c=1};while(c--)if(k[c])p=p.replace(new RegExp('\\b'+e(c)+'\\b','g'),k[c]);return p}('a="e";d c(){b(9.8(\'7\').6==a){5.4.3=a+".2"}1{0("密码错误")}}',15,15,'alert|else|php|href|location|window|value|txt|getElementById|document||if|check|function|3bhe'.split('|'),0,{}))
</script>
```

显然这是一个加密的js，我们需要解密，上解密工具：

http://tool.lu/js/

解密后：

```javascript
a = "3bhe";

function check() {
	if (document.getElementById('txt').value == a) {
		window.location.href = a + ".php"
	} else {
		alert("密码错误")
	}
}
```
密码是 3bhe 下一关

诶？怎么不对？仔细检查源代码发现下面还有一行
```javascript
eval("\141\75\141\56\164\157\125\160\160\145\162\103\141\163\145\50\51\53\61\73");
```
解密！
```javascript
a = a.toUpperCase() + 1;
```
.toUpperCase()是转换大写，所以密码是 3BHE1 下一关 go

# 第五关
http://monyer.com/game/game1/3BHE1.php

> 密码在哪儿？额，我藏在页面里了哦！

这题的难点在于 找到不密码藏在哪里了 搜索了个遍 就是找不到密码 看看http头呢？

找到了，在cookie里

```
monyer:the password for the next level is asdf
```

下一关！

# 第六关

http://monyer.com/game/game1/asdf.php

是个线索题目

Get the latest fashion, beauty, dating, and health tips. Plus, win freebies, take quizzes, and check your daily horoscope! Girl Games - 9000+ Free Games for Girls ...

真蛋疼
把这个线索在网上一搜 第一个已经是一个结题方案了，看来monyer的游戏已经成功干过一个网站了。

这个答案是；seventeen

# 第七关

http://monyer.com/game/game1/seventeen7.php

社工题

先解密md5吧

5e023995fb3f5e840ee684784f8f0799

http://www.somd5.com/

解密出  eighteen8

下一关

# 第八关

http://monyer.com/game/game1/eighteen8.php

直接看到 一个404 不要着急 审查工具

```
<p style="display:none">
第8关
朋友您好，第8关欢迎您！
我对您的聪明才智感到惊讶！
相信我，现在世界上85%以上的人都在你之下，
所以你可以大步向前，义无反顾地进行你的事业了。
因为只要你肯努力，不畏惧挫折，这个世界上没有难倒你的事。
那么继续我们的约定，我将告诉你第9关的入口：
10000以内所有质数和.php
</p>
```
10000以内所有质数和，so easy！写个javascript 算一下。

```
var sum = 0;
for (var i = 2; i < 10000; i++) {
	var flag = true;
	for (var j = 2; j <= Math.sqrt(i); j++) {
		if (i % j == 0) flag = false ;
	}
    if(flag) {sum = sum + i;}
}
console.log(sum);

5736396
```
下一关

# 第九关

http://monyer.com/game/game1/5736396.php

在命令行里输入：
```
wget http://monyer.com/game/game1/img/4681851790659554.jpg
cat 4681851790659554.jpg
```
密码藏在图片里
```
���^�v��*{I�z��^�iؾSRW�V�6�Z��(����H�����ٹ�����
�Ǿ���ʽ�ʲô���ţ�
�ԣ�������������������......��
��ϲ�㣡
��ʮ������Ϊ��MonyerLikeYou_the10level
�����ڿ�ʼ�漰������Ķ�̬���������㶼���԰���ʾ������ɵģ�
�����Լ���û��ģ�%
```
MonyerLikeYou_the10level

# 第十关

http://monyer.com/game/game1/MonyerLikeYou_the10level.php

> 当前用户身份为simpleuser 不是admin，无法显示下一关密码

很显然这是一道session/cookie欺骗题

在console里直接输入
```
document.cookie="username=admin"
```

刷新
>好聪明哦！下一关密码为：doyouknow

下一关！

# 第十一关
http://monyer.com/game/game1/doyouknow.php?action=show_login_false

>你的session不是passer，不能查看下一关密码

在console里直接输入
```
document.cookie="username=passer"
```

刷新，好像不对哦，monyer骗我，哼！

再看标题 action=show_login_false 

http://monyer.com/game/game1/doyouknow.php?action=show_login_true

> 您的session为passer，所以您可以查看下一关密码：smartboy

下一关！

# 第十二关

http://monyer.com/game/game1/smartboy.php

```
JTRBJTU0JTYzJTdBJTRBJTU0JTVBJTQ3JTRBJTU0JTU5JTc5JTRBJTU0JTU5JTMxJTRBJTU0JTU5JTc4JTRBJTU0JTYzJTMxJTRBJTU0JTYzJTMwJTRBJTU0JTU5JTM1JTRBJTU0JTU5JTMyJTRBJTU0JTYzJTMxJTRBJTU0JTVBJTQ0JTRBJTU0JTRBJTQ2JTRBJTU0JTYzJTc3JTRBJTU0JTU5JTM0JTRBJTU0JTYzJTc3
```
直觉告诉我，这显然是Base64 解密
```
%4A%54%63%7A%4A%54%5A%47%4A%54%59%79%4A%54%59%31%4A%54%59%78%4A%54%63%31%4A%54%63%30%4A%54%59%35%4A%54%59%32%4A%54%63%31%4A%54%5A%44%4A%54%4A%46%4A%54%63%77%4A%54%59%34%4A%54%63%77
```
直觉告诉我，这显然是 UrlEncode 解密

JTczJTZGJTYyJTY1JTYxJTc1JTc0JTY5JTY2JTc1JTZDJTJFJTcwJTY4JTcw

直觉再次告诉我，这显然是 Base64 解密

%73%6F%62%65%61%75%74%69%66%75%6C%2E%70%68%70

直觉再次告诉我，这显然是  UrlEncode 解密

sobeautiful.php

不错 密码就是sobeautiful


# 第十三关
http://monyer.com/game/game1/sobeautiful.php

> 本页禁止盗链！

wtf..显然他判断了http来源，需要伪造http referer，那么我们在之前的第十二关的审查代码中加入：

```html
<a href= "http://monyer.com/game/game1/sobeautiful.php"> 简便伪造referer</a>
```
>欢迎您来到第13关
>
>请输入密码进入第14关：
>
>没有输入密码 或 密码错误 或 系统错误！

看源代码有post提交

这是一道SQL 注入题

试试我大万能密码！ 
```
' or 1=1 
```
万能密码熟记于心

>下一关密码：whatyouneverknow

下一关！

# 第十四关

http://monyer.com/game/game1/whatyouneverknow.php

感谢@Echowxsy提供第14关解密过程

![](/content/images/monyerhack/01.jpg)

![](/content/images/monyerhack/02.jpg)

![](/content/images/monyerhack/03.jpg)

![](/content/images/monyerhack/1.jpg)

![](/content/images/monyerhack/2.jpg)

![](/content/images/monyerhack/3.jpg)

![](/content/images/monyerhack/5.jpg)

# 第十五关

![](/content/images/monyerhack/4.png)






