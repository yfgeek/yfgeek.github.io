---
title: 轻松几步让Redux实现数据持久化
date: 2017-11-19 23:02:32
tags: React-Native
categories: Redux  React Redux
header-img: "/content/images/react/wall.png"
---

在开发的过程中，数据用redux管理，觉得希望将数据持久化保存，也就是说当用户下一次打开app或网站的时候，我们希望浏览器/APP自动加载出上次的数据，怎么办？有没有一个🙆‍♂统一的方式？

有的，这就是简单易用的`redux-persist`，事情比想象的简单太多了。

话不多说，上代码！官方示例代码：

```javascript
import { PersistGate } from 'redux-persist/es/integration/react'

import configureStore from './store/configureStore'

const { persistor, store } = configureStore()

const onBeforeLift = () => {
  // take some action before the gate lifts
}

export default () => (
  <Provider store={store}>
    <PersistGate 
      loading={<Loading />}
      onBeforeLift={onBeforeLift}
      persistor={persistor}>
      <App />
    </PersistGate>
  </Provider>
)
```

首先你的结构应该是拥有`reducers`的，在我的代码中，我还加入了`redux-thunk`中间件，如果你不懂，可以忽略这个。

```javascript
 import React, { Component } from 'react';		
 import {AppRegistry} from 'react-native';		
 import { createStore ,applyMiddleware } from 'redux'
 import { Provider } from 'react-redux'		
 import App from "./src/App";		
 import reducers from './src/reducers/reducers'	
 import thunk from 'redux-thunk';		
 export default class CoinOnline extends Component {		
   render() {
     const store = createStore(App,applyMiddleware(thunk));	
     return (
       <Provider store={store}>
        <App />
       </Provider>		
       );
     }
 }
AppRegistry.registerComponent('CoinOnline', () => CoinOnline);       
```

我是怎么应用的？

第一步，引入我们需要的方法

```javascript
import {persistStore, persistCombineReducers} from 'redux-persist';
import { PersistGate } from 'redux-persist/es/integration/react';
```

第二步，重新封装reducer，用`persistCombineReducers()`方法加载配置和reducer

```javascript
const config = {
    key: 'root',
    storage,
};
let reducer = persistCombineReducers(config, reducers);
```

第三步，在redux的`<Provider></Provider>`内层嵌套`<PersistGate></PersistGate>`

```javascript
  <Provider store={store}>
                <PersistGate persistor={persistor}>
                    <App />
                </PersistGate>
</Provider>
```

轻松三步，简单吧！话不多说直接上代码

```jsx

import React, { Component } from 'react';
import {
    AppRegistry,
} from 'react-native';
import { createStore ,applyMiddleware, compose } from 'redux'
import { Provider } from 'react-redux'
import App from "./src/App";
import reducers from './src/reducers/reducers'
import thunk from 'redux-thunk';
import {persistStore, persistCombineReducers} from 'redux-persist';
import { PersistGate } from 'redux-persist/es/integration/react';

import storage from 'redux-persist/es/storage' // default: localStorage if web, AsyncStorage if react-native

const config = {
    key: 'root',
    storage,
};

function configureStore(){
    let reducer = persistCombineReducers(config, reducers);
    let store = createStore(reducer, applyMiddleware(thunk));
    let persistor = persistStore(store);
    return { persistor, store }
}
export default class CoinOnline extends Component {
    render() {
        const { persistor, store } = configureStore();
        return (
            <Provider store={store}>
                <PersistGate persistor={persistor}>
                    <App />
                </PersistGate>
            </Provider>

        );
    }
}
AppRegistry.registerComponent('CoinOnline', () => CoinOnline);

```

多说一句，如果`createStore`有需要加载多个参数，需要用`compose`将其拼装起来。

比如在测试时我还使用了`remote-redux-devtools`调试神器，代码如下

```javascript
let store = createStore(reducer, compose(applyMiddleware(thunk),devToolsEnhancer({ realtime: true, port: 8000 })));
```

下次，每次启动之前`redux-persit`都会默认先`dispatch`两个动作

PERSIT和REHYDRATE，会把上一次的redux中的states注入到当前组件中，即完成了持久化。

![](/content/images/redux-persist/1.png)

![](/content/images/redux-persist/2.png)

