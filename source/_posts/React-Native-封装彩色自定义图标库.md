---
title: React Native 封装彩色自定义图标库
date: 2017-11-21 13:35:05
tags: React-Native
categories: React-Native
header-img: "/content/images/react/wall.png"
---

# 问题描述

本文接上篇[React Native 添加自定义图标库](http://blog.yfgeek.com/2017/11/19/react-native-icon/)，上篇介绍的很基础，但是有没有发现一个问题，通过这种`react-native-vector-icons`方式封装下来的图标，都只有黑白图标，颜色需要自己**手动设置**，但是对于我这种彩色绚丽的图标，手动设置？不存在的...

![](/content/images/react-icon/1.png)

那么本文就在上文的基础上讲讲，我是如何办到的自动彩色图标。

答题思路是创建一个色彩配置文件，等到加载的时候，按照标签去加载。

我Google了一圈，都找不到怎么让他自动加载色彩，可能是我搜索的姿势不对吧。

![](/content/images/react-icon/2-0.png)

# 文件处理

首先，我们在`IcoMoon`先上传并下载一份黑色底色的图标文件，再上传并下载一份彩色底色图标文件。

我们提取出黑色底色图标文件中的`icomoon.ttf`替换掉Xcode中的文件。

与此同时，找到下载的彩色压缩包中找到`selection.json`这个神奇的文件，如果你上传的是彩色图标，实际上，里面内置了所有图标的色彩。

短暂的例子如下：

```javascript
{
  "IcoMoonType": "selection",
  "icons": [
    {
      "icon": {
        "paths": [
          "M1024 512c0 282.77-229.23 512-512 512s-512-229.23-512-512c0-282.77 229.23-512 512-512s512 229.23 512 512z",
          "M495.864 864c-8.635-0.904-17.283-1.694-25.902-2.735-46.907-5.658-90.865-20.248-131.905-43.628-0.617-0.351-1.173-0.807-2.359-1.631 70.607-52.759 140.878-105.267 211.541-158.068 7.173 6.99 14.228 13.852 21.267 20.732 11.824 11.554 23.75 23.006 35.372 34.761 2.857 2.888 4.938 2.843 8.321 1.13 35.662-18.047 64.813-43.452 87.298-76.484 1.076-1.581 2.141-3.168 3.409-5.046 1.007 1.154 1.922 2.078 2.695 3.109 24.557 32.675 49.065 65.386 73.707 97.999 2.131 2.82 1.861 4.498-0.394 7.058-57.072 64.811-127.961 104.761-213.416 118.423-10.764 1.721-21.699 2.371-32.551 3.556-1.443 0.158-2.861 0.543-4.29 0.823h-32.795zM526.886 160c5.987 0.602 11.972 1.234 17.963 1.801 50.406 4.768 97.579 19.719 141.532 44.82 0.618 0.352 1.171 0.814 2.239 1.567-71.758 51.465-143.179 102.689-215 154.198-2.819-2.673-5.592-5.243-8.299-7.879-15.124-14.723-30.288-29.405-45.288-44.253-2.404-2.379-4.231-2.644-7.231-1.144-36.318 18.15-65.87 43.969-88.618 77.568-0.824 1.217-1.649 2.433-3.243 4.784-26.391-35.161-52.444-69.873-78.959-105.2 7.959-8.447 15.318-16.848 23.29-24.622 51.948-50.658 113.581-82.88 185.050-95.845 13.759-2.496 27.819-3.324 41.737-4.951 1.575-0.184 3.129-0.559 4.692-0.845h30.136zM206.697 337.1c53.619 70.288 106.656 139.809 159.841 209.528-10.847 11.122-21.55 22.105-32.261 33.079-8.040 8.239-16.003 16.557-24.194 24.644-2.32 2.291-2.398 4.063-0.994 6.878 18.102 36.324 43.942 65.848 77.544 88.614 1.099 0.745 2.21 1.473 3.287 2.248 0.204 0.146 0.275 0.477 0.859 1.567-34.561 25.915-69.228 51.909-104.355 78.248-62.384-53.699-102.894-119.92-119.15-200.203-17.409-85.972-3.298-167.187 39.424-244.603zM660.291 476.931c9.953-10.34 19.655-20.426 29.366-30.502 8.192-8.501 16.277-17.112 24.668-25.413 2.611-2.584 2.571-4.525 1.042-7.607-18.248-36.766-44.326-66.643-78.406-89.551-1.189-0.8-2.338-1.656-4.339-3.078 35.118-26.354 69.777-52.362 104.912-78.728 59.438 50.901 98.889 113.583 117.002 189.265 22.608 94.462 2.47 187.911-36.701 253.699-52.442-69.266-104.8-138.42-157.545-208.086z"
        ],
        "attrs": [
          {
            "fill": "rgb(48, 44, 44)"
          },
          {
            "fill": "rgb(255, 255, 255)"
          }
        ],
        "isMulticolor": true,
        "isMulticolor2": false,
        "grid": 0,
        "tags": [
          "x"
        ]
      },
      "attrs": [
        {
          "fill": "rgb(48, 44, 44)"
        },
        {
          "fill": "rgb(255, 255, 255)"
        }
      ],
      "properties": {
        "order": 3,
        "id": 66,
        "name": "x",
        "prevSize": 32,
        "code": 59648,
        "codes": [
          59648,
          59649
        ]
      },
      "setIdx": 0,
      "setId": 2,
      "iconIdx": 0
    },
    {...},
   }
}
```

我们分析一下这个文件，不难看出，icons下包含一个数组，每个数组代表每一个图标，每一个数组下都包含属性`tags[0]`,`attrs[0].fill`分别代表对应的标签及色彩。

这个时候，我们需要构建一个自己的色彩配置文件，把刚刚的有用的信息提取出来，具体的按照自己需求，定义标签名字和颜色。

```javascript
let data = require('./selection.json');
let arr = [];
data.icons.forEach((item)=>{
	arr.push(
	{
		'cuy' : item.icon.tags[0],
		'color' :item.icon.attrs[0].fill,
	})
});
console.log(arr);
```

把输出的信息复制到新的文件，并将其命名`config.js`

```javascript
export const ColorConfig = [
    { cuy: 'x', color: 'rgb(48, 44, 44)' },
    { cuy: 'ada', color: 'rgb(60, 200, 200)' },
    { cuy: 'amp', color: 'rgb(45, 174, 228)' },
    { cuy: 'ant', color: 'rgb(44, 211, 225)' },
    { cuy: 'ardr', color: 'rgb(60, 135, 199)' },
    { cuy: 'ark', color: 'rgb(247, 0, 0)' },
    { cuy: 'bat', color: 'rgb(255, 80, 0)' },
    { cuy: 'bcc', color: 'rgb(247, 147, 28)' },
    { cuy: 'bch', color: 'rgb(247, 147, 26)' },
    { cuy: 'bcn', color: 'rgb(240, 64, 134)' },
    { cuy: 'bnb', color: 'rgb(243, 186, 47)' },
    { cuy: 'btc', color: 'rgb(247, 147, 26)' },
    { cuy: 'btcd', color: 'rgb(255, 102, 0)' },
    { cuy: 'bts', color: 'rgb(53, 186, 235)' },
    { cuy: 'dash', color: 'rgb(28, 117, 188)' },
    { cuy: 'dcr', color: 'rgb(46, 214, 161)' },
    { cuy: 'dgb', color: 'rgb(0, 106, 210)' },
    { cuy: 'dgd', color: 'rgb(244, 208, 41)' },
    { cuy: 'doge', color: 'rgb(195, 166, 52)' },
    { cuy: 'emc', color: 'rgb(180, 159, 252)' },
    { cuy: 'eos', color: 'rgb(180, 159, 252)' },
    { cuy: 'etc', color: 'rgb(102, 144, 115)' },
    { cuy: 'eth', color: 'rgb(98, 126, 234)' },
    { cuy: 'fct', color: 'rgb(65, 123, 164)' },
    { cuy: 'game', color: 'rgb(152, 192, 31)' },
    { cuy: 'gbyte', color: 'rgb(48, 44, 44)' },
    { cuy: 'gno', color: 'rgb(0, 166, 196)' },
    { cuy: 'gnt', color: 'rgb(0, 29, 87)' },
    { cuy: 'hsr', color: 'rgb(86, 66, 142)' },
    { cuy: 'icn', color: 'rgb(76, 111, 140)' },
    { cuy: 'kmd', color: 'rgb(50, 100, 100)' },
    { cuy: 'knc', color: 'rgb(24, 140, 146)' },
    { cuy: 'lkk', color: 'rgb(157, 1, 235)' },
    { cuy: 'lsk', color: 'rgb(2, 136, 209)' },
    { cuy: 'ltc', color: 'rgb(203, 198, 198)' },
    { cuy: 'maid', color: 'rgb(85, 146, 215)' },
    { cuy: 'miota', color: 'rgb(36, 36, 36)' },
    { cuy: 'mln', color: 'rgb(11, 21, 41)' },
    { cuy: 'mona', color: 'rgb(240, 219, 176)' },
    { cuy: 'neo', color: 'rgb(0, 0, 0)' },
    { cuy: 'nlg', color: 'rgb(42, 176, 253)' },
    { cuy: 'nmc', color: 'rgb(24, 108, 157)' },
    { cuy: 'nxt', color: 'rgb(0, 143, 187)' },
    { cuy: 'omg', color: 'rgb(26, 83, 240)' },
    { cuy: 'pay', color: 'rgb(48, 44, 44)' },
    { cuy: 'pivx', color: 'rgb(94, 71, 120)' },
    { cuy: 'pot', color: 'rgb(16, 91, 47)' },
    { cuy: 'ppc', color: 'rgb(60, 176, 84)' },
    { cuy: 'ppt', color: 'rgb(21, 39, 67)' },
    { cuy: 'qtum', color: 'rgb(46, 154, 208)' },
    { cuy: 'rep', color: 'rgb(96, 42, 82)' },
    { cuy: 'salt', color: 'rgb(27, 238, 244)' },
    { cuy: 'sc', color: 'rgb(0, 203, 160)' },
    { cuy: 'sngls', color: 'rgb(179, 13, 35)' },
    { cuy: 'steem', color: 'rgb(75, 162, 242)' },
    { cuy: 'strat', color: 'rgb(19, 135, 201)' },
    { cuy: 'trx', color: 'rgb(0, 0, 0)' },
    { cuy: 'usdt', color: 'rgb(38, 161, 123)' },
    { cuy: 'vtc', color: 'rgb(53, 99, 62)' },
    { cuy: 'waves', color: 'rgb(0, 150, 220)' },
    { cuy: 'xcp', color: 'rgb(237, 22, 80)' },
    { cuy: 'xem', color: 'rgb(103, 178, 232)' },
    { cuy: 'xlm', color: 'rgb(20, 182, 231)' },
    { cuy: 'xmr', color: 'rgb(255, 102, 0)' },
    { cuy: 'xrp', color: 'rgb(0, 170, 228)' },
    { cuy: 'xtz', color: 'rgb(166, 224, 0)' },
    { cuy: 'zec', color: 'rgb(236, 178, 68)' }
    ];
```

这样，色彩配置文件封装好了。

# 加载色彩

按照上文所做，我们封装出自己的图标库，当调用到图标组建的时候，我们这么加载它：

 ```json
import {ColorConfig} from '../icon/config';
render(){
let color = '#f5922f';
ColorConfig.forEach((item,index)=>{if(item.cuy === cuy)  color = item.color});
  return <Icon name={cuy} size={48} color={color}/>;
}
 ```

# 补全镂空

理论上现在就结束了，但是我发现了这样做的一个问题，如图所示

![](/content/images/react-icon/2-1.png)

其实解决这个问题相当简单，只需要多加一层View，并将其底色设置为白色，切角设置为50%的圆（web端思路）

但是，在实际运用的时候，发现`borderRadius`无法设置为`50%`，其实也不难，代码如下

```json
import {ColorConfig} from '../icon/config';
render(){
let color = '#f5922f';
let w = this.props.width || 48;
ColorConfig.forEach((item,index)=>{if(item.cuy === cuy)  color = item.color});
  return (
                <View style={{
                    alignItems:'center',
                    justifyContent:'center',
                    borderRadius: w/2,
                    overflow: 'hidden',
                    backgroundColor : '#ffffff',
                }}>
                   <Icon name={cuy} size={w} color={color}/>;
                </View>
        )
}
```

再看看效果，完美！

![](/content/images/react-icon/2-2.png)

![](/content/images/react-icon/2-3.png)

# 效率问题

这样手动封装的库，实际上会引发一些效率问题，比如在加载众多组建的时候，每一个组件都会重新读取一边色彩配置文件，并将其放入内存，实际上意义不大，浪费时间。

实际上，我们可以把色彩配置文件和`selection.json`一并放在`store`中，或者放在最顶层的组件的`state`中，或者存成hashmap形式，内存只需要加载一次配置文件就够了。

Anyway，这种方法有待优化，但是对于图标数量比较小的APP来说，优化的意义不大。