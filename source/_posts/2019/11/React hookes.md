---
title: React hookes 学习笔记
date: 2019-11-16 22:10:17
categories: react
top: 100
tags:
    - react
    - 学习笔记和总结
photos: 
    - "https://desk-fd.zol-img.com.cn/t_s1440x900c5/g5/M00/0C/00/ChMkJ13VHGeIQbMvAAZiH-yBkAsAAvVdQCGfhkABmI3388.jpg"
---
<!--more-->
## React hookes
### 基本要求：
1. 只能在函数式组件中进行书写
2. 只能在顶层调用Hooks。不要在循环，条件或嵌套函数中调用Hook。比如在if/eles 中就不能进行书写。

### 方法： 
#### useState 保存组件状态
```js
 import { useState } from 'react';

function Example() {
  const [count, setCount] = useState(0);
  //const [age, setAge] = useState(42);
  //const [fruit, setFruit] = useState('banana');
  //const [todos, setTodos] = useState([{ text: 'Learn Hooks' }]);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```
+ useState的参数就是以前state的初始值。
+ useState返回的值中第一个参数是以前的state,第二个参数是setState,不过以前我们只有一个state,现在可以自由命名，更直观了，比如上面的age和setAge，fruit和setFruit

>值得注意的是 useState 不帮助你处理状态，相较于 setState 非覆盖式更新状态，useState 覆盖式更新状态，需要开发者自己处理逻辑。

参考下面的代码：
```js
import React from "react";
class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0,
      name: "alife"
    };
  }
  render() {
    const { count } = this.state;
    return (
      <div>
        Count: {count}
        <button onClick={() => this.setState({ count: count + 1 })}>+</button>
        <button onClick={() => this.setState({ count: count - 1 })}>-</button>
      </div>
    );
  }
}
```
使用useState后

```js
import React, { useState } from "react";
function App() {
  const [obj, setObject] = useState({
    count: 0,
    name: "alife"
  });
  return (
    <div className="App">
      Count: {obj.count}
      <button onClick={() => setObject({ ...obj, count: obj.count + 1 })}>+</button>
      <button onClick={() => setObject({ ...obj, count: obj.count - 1 })}>-</button>
    </div>
  );
}
```
从useState的例子可以看出，我们在setObject中`{...obj,count:obj.count+1}`对其进行了一个全覆盖，相较于 setState是 非覆盖式更新状态，useState覆盖式更新状态，需要我们处理逻辑。

#### useEffect 处理副作用
为了直观的看出useEffect的用法和作用，我们将下面的例子改成用hooks的例子。

```js
import React, { Component } from "react";
class App extends Component {
  state = {
    count: 1
  };
  componentDidMount() {
    const { count } = this.state;
    document.title = "componentDidMount" + count;
    this.timer = setInterval(() => {
      this.setState(({ count }) => ({
        count: count + 1
      }));
    }, 1000);
  }
  componentDidUpdate() {
    const { count } = this.state;
    document.title = "componentDidMount" + count;
  }
  componentWillUnmount() {
    document.title = "componentWillUnmount";
    clearInterval(this.timer);
  }
  render() {
    const { count } = this.state;
    return (
      <div>
        Count:{count}
        <button onClick={() => clearInterval(this.timer)}>clear</button>
      </div>
    );
  }
}
```
在例子中，组件每隔一秒更新组件状态，并且每次触发更新都会触发 document.title 的更新(副作用)，而在组件卸载时修改 document.title（类似于清除）

从例子中可以看到，一些重复的功能开发者需要在 componentDidMount 和 componentDidUpdate 重复编写，而如果使用 useEffect 则完全不一样

```js
import React, { useState, useEffect } from "react";
let timer = null;
function App() {
  const [count, setCount] = useState(0);
  useEffect(() => {
    document.title = "componentDidMount" + count;
  },[count]);
 
  useEffect(() => {
    timer = setInterval(() => {
      setCount(prevCount => prevCount + 1);
    }, 1000);
    return () => {
      document.title = "componentWillUnmount";
      clearInterval(timer);
    };
  }, []);
  return (
    <div>
      Count: {count}
      <button onClick={() => clearInterval(timer)}>clear</button>
    </div>
  );
}
```
将上面的例子用使用 useEffect 重写了上面的例子，useEffect 第一个参数传递函数，可以用来做一些副作用比如异步请求，修改外部参数等行为，而第二个参数是个数组，如果数组中的值改变才会触发 useEffect 第一个参数中的函数。返回值(如果有)则在组件销毁或者调用函数前调用。

1. 比如第一个 useEffect 中，理解起来就是一旦 count 值发生改变，则修改 documen.title 值
2. 而第二个 useEffect 中数组没有传值，代表不监听任何参数变化，即只有在组件初始化或销毁的时候才会触发，用来代替 componentDidMount 和 componentWillUnmount

参考文章：

[十个案例学会 React Hooks](https://lindongzhou.com/article/learn-react-hooks)

[对React Hooks的一些思考](https://zhuanlan.zhihu.com/p/48264713)

