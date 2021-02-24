---
title: 我眼中的React
date: 2017-02-04 14:35:03
tags: Web
categories: Web
header-img: "/content/images/react/wall.png"

---

前两天，用React把 之前的 [基于树莓派3B,DHT11/DHT22,LCD1602的一个实时温度湿度检测系统](https://github.com/yfgeek/rpi-TempRuntime) 的Web部分重构了一下。

这个项目之前是用Semantic UI + eCharts + JQuery Datapicker写的，由于这个项目的实现过程比较经典，几乎把所有的感觉React的基础思想都实现了一遍。

无疑，React给前端带来了各种新的想法与思想，声明式、虚拟Dom、单向数据流、JSX、组件化、Flux、以JavaScript为中心、React Native。

![](/content/images/react/0.png)

# 虚拟Dom
React是以JS为中心的，而非HTML，虚拟Dom是一个至关重要的概念。

在jQuery出现之前，我们一般都是直接操作Dom，方法“暴力、简单”，也存在兼容性的问题。在jQuery出现之后，jQuery的选择器帮助我们简单、高效的操作Dom，也就是在jQuery出现之后，前端就开始大爆发了。

但是随着发展，这种简单暴力的操作Dom的方法对于很多大型系统性工程来说，从软件工程的角度上讲，是杂乱、无序的，一旦时间久远，项目越来越大，维护成本就变大。于是，我们有了各种前端MVC、MVVM框架，典型的有Angular、React、vue.js，MVVM给我们带来一种全新的思想，数据绑定。

不过，与此同时，React给我们带来了一种全新的思想，虚拟Dom，从此，我们再也不用直接操作Dom了，而是用虚拟Dom更新Dom。

你可能会问，这样做，效率岂不是更低了？

是的，也不是。其实没有那么明显，一般来讲，操作Dom树的Diff算法应该是O(n^3)，而React把效率降低到了O(n)。

![](/content/images/react/1.png)

简单来讲，如图所示，React只会比较相同层级的Dom，从而简化了Dom Diff的复杂度，虽然有所舍弃，但是全局比较是没有必要的。

通过在JSX文件中写虚拟Dom，React在内存中会自动拿现在的虚拟Dom和旧的虚拟Dom进行Diff操作，如果发现有Diff的部分，就拿出来形成新的Patch部分，加入到现在的Dom树上。

也就是因为这个原因，虚拟Dom使得在操作Dom上更加高效，每次更新数据不会进行O(n^3)复杂的操作。

你可能会问，什么是JSX？

# JSX

其实JSX只是一种未编译的JavaScript，可以方便的对HTML进行书写，先上代码：

```javascript
import React from 'react';
import { Button } from 'semantic-ui-react';

class ButtonComponent extends React.Component {
    render() {
        return(
            <span>
            <Button color='blue' >分钟</Button>
            <Button color='violet'>小时</Button>
            </span>
        );
    }
}

export default ButtonComponent;
```
代码部分用`ES6`书写，你可能会问什么是ES6？其实还有ES7(ECMAScript 2016)呢~

那么，你可能看到了很变态的写法，就是HTML和JS混写，没错，这就是EJS，但请注意，这不是HTML，写着写着很有可能会掉入[JSX陷阱](http://www.css88.com/react/docs/jsx-gotchas.html)。

经过`babel`等工具的编译后，JSX在运行的时候会被编译为真实的JavaScript语言，之所以这样写，只是为了方便程序员更方便的写代码。

比如上面的代码“HTML部分”会被编译为：

```javascript
React.createElement('button', {class: 'ui blue button'}, '分钟');
React.createElement('button', {class: 'ui violet button'}, '小时');
```

当然，JSX也有很多坑，比如：

`Module build failed: SyntaxError: Adjacent JSX elements must be wrapped in an enclosing tag`

作为新手，这个问题，我经常遇到，因为你在写每一个JSX模块的时候，你需要用类似于``<div></div>``作为开始与结尾，不规范的代码不会被编译成功。

# ECMAScript(ES)

ES无疑是这几年最火的前端语言，没错，他是JavaScript的改进版，我知道，其实还有很多其他类似的比如CoffeeScript，但是ES会被各大浏览器所兼容支持，但是你可能会问到兼容性问题，没事，我们有`babel`工具。

* ECMAScript：一个由 ECMA International 进行标准化，TC39 委员会进行监督的语言。通常用于指代标准本身。
* JavaScript：ECMAScript 标准的各种实现的最常用称呼。这个术语并不局限于某个特定版本的 ECMAScript 规范，并且可能被用于任何不同程度的任意版本的 ECMAScript 的实现。
* ECMAScript 5 (ES5)：ECMAScript 的第五版修订，于 2009 年完成标准化。这个规范在所有现代浏览器中都相当完全的实现了。
* ECMAScript 6 (ES6) / ECMAScript 2015 (ES2015)：ECMAScript 的第六版修订，于 2015 年完成标准化。这个标准被部分实现于大部分现代浏览器。可以查阅这张兼容性表来查看不同浏览器和工具的实现情况。
* ECMAScript Proposals：被考虑加入未来版本 ECMAScript 标准的特性与语法提案，他们需要经历五个阶段：Strawman（稻草人），Proposal（提议），Draft（草案），Candidate（候选）以及 Finished （完成）。
* ECMAScript 2016：第七版 ECMAScript 修订，增加了两个新特性。

以上，都是我们常说的ES，在网上看到的代码目前以ES5和ES6居多，写法各异，我建议用最新版的写法写代码。

说白了，就是Javascript并不是一个优秀的语言，但是经过不断的填补，修正，已经让Javascript支持了很多新特性，新的语法，更多的书写方式，以及拥有了更严谨的编程思想。比如``let``,arrow function(就是这个：`(i) => i + 1` )在ES中的运用等。

所以，在认识React之前，最好学习ES的新特性以及[常用的新特性](http://blog.csdn.net/zhangjjie0110/article/details/53782537)。

# 组件化

我觉得这是React的重量级思想，组件化前端，从此，前端再也不是东拼西凑，而是像积木一样，拼起来。

![](/content/images/react/2.png)

React把单一的用户界面，拆成了各种各样的组件，形成“组件树”，采用分而治之的思想，有一个好处，就是维护起来极其方便，对于组件的修改也显得很容易。

# 数据绑定

众所周知，Angular是双向数据绑定的，并且引以为豪，因为更容易维护与理解。

但是双向数据绑定也带来了许多问题，会在些其他功能的时候莫名其妙出现很多bug。

React是单向数据绑定的，是非常稳定的做法，但是也存在一些问题，就是在操作其他组件的时候，对于数据流的处理，显得异常困难。

在React中，数据绑定仅仅需要在`render`中写`{数据绑定}`，而非像Angular一样，两边都要设置，一定情况下讲，这是比较方便的。


# 数据模型

## state

话说回来，React的每一个组件的实质是什么？状态机（State Machines），在React的每一个组件里，通过更新`this.state`，再通过`render()`进行渲染，React会自动把最新的状态渲染到网页上。

举个栗子🌰
```javascript
class DateSelectorComponent extends Component {

    constructor(props) {
    super(props);
    let todaydate = this.showLocale(new Date());
     this.state = {
         timenow: todaydate,
         showCalendar: false
    }
            return(
            <div>
            <Input icon='calendar' iconPosition='left' readOnly onClick={this.showCalendar} value={this.state.timenow} ref="inputbox"/>
            <ButtonComponent />
            {CalendarComponent}
            </div>
        )
}
```
通过这样的方式，我就设置了state的默认初始值，默认日历选择器是不出现的，而今天的日期是今天日期，这很正常。然后在将所谓的值渲染到Dom上。

## props

React的数据流是单向的，是自上向下的层级传递的，props可以对固定的数据进行传递。

```javascript
class HelloMessage extends Component {
  render( 
    return <h1>Hello {this.props.name}</h1>;
    )
}

class MainPage extends Component {
render(){
 return(
  <HelloMessage name="John" />
  )
 }
}
```
在这种情况下，子组件会自动读取出父组件传递过来的`props`值John，并快速的渲染在页面上。

## 到底谁是谁

`state`和`props`看起来很相似，其实是完全不同的东西。

| 问题             | _props_ | _state_ |      |
| -------------- | ------- | ------- | ---- |
| 可以从父组件得到初始值吗？  | 可以      | 可以      |      |
| 可以被父组件改变值吗？    | 可以      | 不可以     |      |
| 可以设置组件内部的初始值吗？ | 可以      | 可以      |      |
| 可以改变组件内部的初始值吗？ | 不可以     | 可以      |      |
| 可以设置子组件的初始值吗？  | 可以      | 可以      |      |
| 可以改变子组件的初始值吗？  | 可以      | 不可以     |      |

按照我的个人理解，`props`是静态的，存入不变量，比如 购物车里的 商品名称、价格；`state`是动态的，存入随时变化的量，比如 用户购买商品的总价，购买数目。

从操作的角度上讲，`props`是单向传递的，会一直被传递到子组件，而`state`更倾向于自身，只能改变自身的值。

# 获取Dom

``this.refs``是react的重要组成之一，通过该方法可以快速高效的获取Dom。

例如，在JSX代码中这样写道
```html
  <input ref="myInput" />
```
便可以通过
```javascript
  this.refs.myInput.value
```
命令获取该Input下的dom属性的value值，非常方便。

# 生命周期

组件都是有生命周期的，生命周期内，`props`和`state`改变会导致React自动用Diff算法重新渲染页面。那么生命周期到底都有哪些呢？

大体上分为三类：

* 挂载： 组件被插入到DOM中。
* 更新： 组件被重新渲染，查明DOM是否应该刷新。
* 移除： 组件从DOM中移除。

从流程上讲，是这样的：

**挂载期：**

getInitialState() -->> componentWillMount() -->> render() -->> componentDidMount()

**更新期：**

componentWillReceiveProps() -->> shouldComponentUpdate() -->> componentWillUpdate render() -->> componentDidUpdate()

**移除期：**

componentWillUnmount()


## 挂载

**componentWillMount()**： 在初次渲染之前执行一次，最早的执行点

**componentDidMount()**： 在初次渲染之后执行，比较常用，比如持续执行某事件：

```JavaScript
    componentDidMount(){
     setInterval(this.loadData(this.state.date),1000);
    }
```

## 更新

**componentWillReceiveProps()**： 在组件接收到新的 `props` 的时候调用。在初始化渲染的时候，该方法不会调用。

**shouldComponentUpdate()**： 在接收到新的 `props` 或者 `state`，将要渲染之前调用。

**componentWillUpdate()**： 在接收到新的 props 或者 state 之前立刻调用。

**componentDidUpdate()**： 在组件的更新已经同步到 DOM 中之后立刻被调用。

## 移除

**componentWillUnmount()**： 在组件从 DOM 中移除的时候立刻被调用。


# Flux

React是MVC中V的一部分，而Flux则是M和C的部分，Flux是单向数据流，符合React的核心思想，不过，Flux并不完善，是一个很松散的架构。

* Dispatcher： 处理动作分发，维护 Store 之间的依赖关系
* Store： 数据和逻辑部分
* View： React 组件，这一层可以看作 controller-views，作为视图同时响应用户交互
* Action： 提供给 Dispatcher 传递数据给 Store

![](/content/images/react/3.png)


# Redux

![](/content/images/react/5.jpeg)

Flux把总架构都搭好了，可是实现起来并不容易，因为Flux并没有强大的API，只是一种纯粹的思想实现，而Redux是Flux的“升级版”，把各个部分更加方便的实现起来。


此部分未完待续

# 总结

我觉得React给前端开发带来了一种全新的思想，那就是以JS为中心，一种全新的世界。

网页不再是网页，而是像工程一样，一块块搭建起来的，拆下来，搬上去都很容易，似的前端再也不是那个凌乱拼凑的年代。

我想任何一种编程语言，无论是解释性、脚本、编译语言都逃离不了这个全新的年代，就是组件化，大家似乎都在不同方向发展，但实际上，确实一样的。

# 相关阅读

[深入浅出React（四）：虚拟DOM Diff算法解析](http://www.infoq.com/cn/articles/react-dom-diff)

[React/React Native 的ES5 ES6写法对照表](http://bbs.reactnative.cn/topic/15/react-react-native-%E7%9A%84es5-es6%E5%86%99%E6%B3%95%E5%AF%B9%E7%85%A7%E8%A1%A8/2)

[React组件声明周期过程说明](https://www.cllgeek.com/archives/544)