---
title: React Native 添加自定义图标库
date: 2017-11-20 16:47:37
tags: React-Native
categories: React-Native
header-img: "/content/images/react/wall.png"
---

最近在开发一个React Native小程序，遇到了很多问题，好在都解决了，近期将逐步增加一些解决问题式文章，方便各位网友学习，如有不对，请多指教！

本文讲述用`react-native-vector-icons`库，封装一个自己的图标库，也就是说添加自定义图标库，下面讲讲如何添加。

通俗易懂，而且本文只讲一种方式，方便各位网友学习，我讲的方式就是最简单的`createIconSetFromIcoMoon`方法。

第一步，准备好你的素材，最好是`svg`格式

第二步，进入网址：https://icomoon.io/app/#/select

点击Import Icons按钮，将图标**批量**导入，如图下所示：

![](/content/images/react-icon/1.png)

第三步，点击Generate Font按钮。

第四步，点击Download按钮。

这时候，你会下载到如图所示结构的zip文件，将其解压，拷贝到项目根目录。

![](/content/images/react-icon/2.png)

第五步，在Xcode中添加压缩包里的`icomoon.ttf`文件。

用Xcode打开工程文件，将`icomoon.ttf`文件复制一份，并拖动到Resources目录中，如图所示

![](/content/images/react-icon/3.png)

第六步，点开`Info.plist`，在`Fonts provided by application`属性中添加这个字体的名称`icomoon.ttf`的键值。

![](/content/images/react-icon/4.png)

第七步，在项目配置中的Build Phases中的Copy Bundle Resources 中添加该字体。

![](/content/images/react-icon/8.png)



第七步，在项目根目录下，运行终端命令：

```bash
npm install react-native-vector-icons --save
react-native link
```

![](/content/images/react-icon/5.png)

第八步，配置安卓：

编辑 `android/app/build.gradle` ，添加如下内容，可以说非常简单

```js
project.ext.vectoricons = [
    iconFontNames: [ 'icomoon.ttf'] 
]
apply from: "../../node_modules/react-native-vector-icons/fonts.gradle"
```

第八步，在代码中写：

```javascript
import {createIconSetFromIcoMoon } from 'react-native-vector-icons';

import icoMoonConfig from '../icon/selection.json';

const Icon = createIconSetFromIcoMoon(icoMoonConfig);

export default Iconfontello;
```

第九步，作为组件调用

```jsx
<Icon name="btc" size={32} />;
```

