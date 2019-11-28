---
title: React context基本用法总结
date: 2019-11-14 23:10:17
categories: react 
top: 100
tags:
    - react
    - 学习笔记和总结
photos: 
    - "https://desk-fd.zol-img.com.cn/t_s1024x768c5/g5/M00/0C/02/ChMkJ13V9q6IOSp_AAPNPlvQOwwAAvV_QJYgfoAA81W947.jpg"
---

## 前言
`Context`被翻译为上下文，在编程领域，这是一个经常会接触到的概念，React中也有。

在`React`的官方文档中，`Context`被归类为高级部分`(Advanced)`，属于React的高级API，但官方并不建议在稳定版的App中使用`Contex`。

不过，这并非意味着我们不需要关注`Context`。事实上，很多优秀的`React`组件都通过`Context`来完成自己的功能，比如`react-redux的<Provider />`，就是通过`Context`提供一个全局态的`store`，拖拽组件r`eact-dnd`，通过`Context`在组件中分发`DOM`的`Drag`和`Drop`事件，路由组件`react-router`通过`Context`管理路由状态等等。在React组件开发中，如果用好Context，可以让你的组件变得强大，而且灵活。

### 初识React Context
当你不想在组件树中通过逐层传递`props`或者`state`的方式来传递数据时，可以使用Context来实现跨层级的组件数据传递。
![](https://upload-images.jianshu.io/upload_images/1457831-b19e007758f57df7?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp
)
使用`props`或者`state`传递数据，数据自顶下流。
![](https://upload-images.jianshu.io/upload_images/1457831-1b8e3c5ce88f7758?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)
使用Context，可以跨越组件进行数据传递。

### 如何使用Context
如果要Context发挥作用，需要用到两种组件，一个是`Context生产者(Provider)`，通常是一个父节点，另外是一个`Context的消费者(Consumer)`，通常是一个或者多个子节点。所以Context的使用基于生产者消费者模式。

对于父组件，也就是Context生产者，需要通过一个静态属性`childContextTypes`声明提供给子组件的Context对象的属性，并实现一个实例g`etChildContext`方法，返回一个代表Context的纯对象 (plain object) 。
```
import React from 'react'
import PropTypes from 'prop-types'

class MiddleComponent extends React.Component {
  render () {
    return <ChildComponent />
  }
}

class ParentComponent extends React.Component {
  // 声明Context对象属性
  static childContextTypes = {
    propA: PropTypes.string,
    methodA: PropTypes.func
  }
  
  // 返回Context对象，方法名是约定好的
  getChildContext () {
    return {
      propA: 'propA',
      methodA: () => 'methodA'
    }
  }
  
  render () {
    return <MiddleComponent />
  }
}
```
而对于Context的消费者，通过如下方式访问父组件提供的Context。
```
import React from 'react'
import PropTypes from 'prop-types'

class ChildComponent extends React.Component {
  // 声明需要使用的Context属性
  static contextTypes = {
    propA: PropTypes.string
  }
  
  render () {
    const {
      propA,
      methodA
    } = this.context
    
    console.log(`context.propA = ${propA}`)  // context.propA = propA
    console.log(`context.methodA = ${methodA}`)  // context.methodA = undefined
    
    return ...
  }
}
```
子组件需要通过一个静态属性`contextTypes`声明后，才能访问父组件`Context`对象的属性，否则，即使属性名没写错，拿到的对象也是`undefined`。

对于无状态子组件(Stateless Component)，可以通过如下方式访问父组件的Context
```
import React from 'react'
import PropTypes from 'prop-types'

const ChildComponent = (props, context) => {
  const {
    propA
  } = context
    
  console.log(`context.propA = ${propA}`)  // context.propA = propA
    
  return ...
}
  
ChildComponent.contextProps = {
  propA: PropTypes.string    
}
```
而在接下来的发行版本中，React对Context的API做了调整，更加明确了生产者消费者模式的使用方式。
```
import React from 'react';
import ReactDOM from 'react-dom';

const ThemeContext = React.createContext({
  background: 'red',
  color: 'white'
});
```
通过静态方法`React.createContext()`创建一个Context对象，这个Context对象包含两个组件，`<Provider />和<Consumer />`。
```
class App extends React.Component {
  render () {
    return (
      <ThemeContext.Provider value={{background: 'green', color: 'white'}}>
        <Header />
      </ThemeContext.Provider>
    );
  }
}
```
`<Provider />`的value相当于现在的`getChildContext()`。
```
class Header extends React.Component {
  render () {
    return (
      <Title>Hello React Context API</Title>
    );
  }
}
 
class Title extends React.Component {
  render () {
    return (
      <ThemeContext.Consumer>
        {context => (
          <h1 style={{background: context.background, color: context.color}}>
            {this.props.children}
          </h1>
        )}
      </ThemeContext.Consumer>
    );
  }
}
```
`<Consumer />`的children必须是一个函数，通过函数的参数获取`<Provider />提供的Context`。
可见，Context的新API更加贴近React的风格。

### 总结：
React的context就是一个全局变量，可以从根组件跨级别在React的组件中传递。React context 的API 有两个版本,React16.x 之前 的是老版本的 context，之后的是新版本的context。
#### 1.老版本的context
`getChildContext `根组件中声明，一个函数，返回一个对象,就是context `childContextTyp`e根组件中声明,指定context的结构类 型，如不指定,会产生错误
`contextTypes `子孙组件中声明，指定要接收的context的结构类型，可以只是context的一部分结构。contextTypes 没有定义，context将是一个空对象。
`this.context` 在子孙组件中通过此来获取上下文
> (注:从React v15.5开始 ，React.PropTypes 助手函数已被弃用，可使用 prop-types 库 来定义contextTypes)

举例如下：
```
//根组件
class MessageList extends React.Component {
  getChildContext() {
    return {color: "purple",text: "item text"};
  }

  render() {
    const children = this.props.messages.map((message) =>
      <Message text={message.text} />
    );
    return <div>{children}</div>;
  }
}

MessageList.childContextTypes = {
  color: React.PropTypes.string
  text: React.PropTypes.string
};

//中间组件
class Message extends React.Component {
  render() {
    return (
      <div>
        <MessageItem />
        <Button>Delete</Button>
      </div>
    );
  }
}

//孙组件(接收组件)
class MessageItem extends React.Component {
  render() {
    return (
      <div>
        {this.context.text}
      </div>
    );
  }
}

MessageItem.contextTypes = {
  text: React.PropTypes.string
};

class Button extends React.Component {
  render() {
    return (
      <button style={{background: this.context.color}}>
        {this.props.children}
      </button>
    );
  }
}

Button.contextTypes = {
  color: React.PropTypes.string
};

```
#### 2.新版本的context
新版本的React context使用了Provider和Customer模式，和react-redux的模式非常像。在顶层的Provider中传入value，
在子孙级的Consumer中获取该值，并且能够传递函数，用来修改context，如下代码所示：
```
//创建Context组件
const ThemeContext = React.createContext({
  theme: 'dark',
  toggle: () => {}, //向上下文设定一个回调方法
});

//运行APP
class App extends React.Component {
  constructor(props) {
    super(props);

    this.toggle = () => { //设定toggle方法，会作为context参数传递
      this.setState(state => ({
        theme:
          state.theme === themes.dark
            ? themes.light
            : themes.dark,
      }));
    };

    this.state = {
      theme: themes.light,
      toggle: this.toggle,
    };
  }

  render() {
    return (
      <ThemeContext.Provider value={this.state}> //state包含了toggle方法
        <Content />
      </ThemeContext.Provider>
    );
  }
}

//中间组件
function Content() {
  return (
    <div>
      <Button />
    </div>
  );
}

//接收组件
function Button() {
  return (
    <ThemeContext.Consumer>
      {({theme, toggle}) => (
        <button
          onClick={toggle} //调用回调
          style={{backgroundColor: theme}}>
          Toggle Theme
        </button>
      )}
    </ThemeContext.Consumer>
  );
}
```
详细用法可以参考官方文档：https://react.docschina.org/docs/context.html#reactcreatecontext

#### 3. context在如下的生命周期钩子中可以使用
```
constructor(props, context)
componentWillReceiveProps(nextProps, nextContext)
shouldComponentUpdate(nextProps, nextState, nextContext)
componentWillUpdate(nextProps, nextState, nextContext)
componentDidUpdate(prevProps, prevState, prevContext)
```
#### 4. 在无状态组件中可以通过参数传入
```
function D(props, context) {
  return (
    <div>{this.context.user.name}</div>
  );
}

D.contextTypes = {
  user: React.PropTypes.object.isRequired
}

```
#### 5. React context的局限性

1. 在组件树中，如果中间某一个组件 `ShouldComponentUpdate returning false` 了，会阻碍 context 的正常传值，导致子组件无法获取更新。
2. 组件本身 `extends React.PureComponent` 也会阻碍 context 的更新。

> 注意点：
> 1. Context 应该是唯一不可变的. 
> 2. 组件只在初始化的时候去获取 Context

参考: 
1. [https://www.tuicool.com/articles/nUryimf](https://www.tuicool.com/articles/nUryimf)
2. [https://segmentfault.com/a/1190000012575622](https://segmentfault.com/a/1190000012575622)
3. [https://www.jianshu.com/p/eba2b76b290b](https://www.jianshu.com/p/eba2b76b290b)