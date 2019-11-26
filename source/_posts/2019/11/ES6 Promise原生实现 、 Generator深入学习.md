---
title: ES6 Promise原生实现 、 Generator深入学习
date: 2019-11-13 23:10:17
categories: js
top: 100
tags:
    - js
    - 学习笔记和总结
photos: 
    - "https://desk-fd.zol-img.com.cn/t_s1024x768c5/g5/M00/0C/02/ChMkJ13V9q6IOSp_AAPNPlvQOwwAAvV_QJYgfoAA81W947.jpg"
---
## 11-13 ES6 Promise原生实现 、 Generator深入学习

### 了解

1. Promise 的局限性。

2. 原生实现 、其一些细节。

3. Generator 自执行函数的实现。
<!--more-->

## 

### ES6 Promise 

#### Promise 的局限性

1. **错误被吃掉**

首先我们要理解，什么是错误被吃掉，是指错误信息不被打印吗？

并不是，举个例子：
```js
throw new Error('error');
console.log(233333);
```
在这种情况下，因为 `throw error `的缘故，代码被阻断执行，并不会打印 233333，再举个例子：
```js
const promise = new Promise(null);
console.log(233333);
```
以上代码依然会被阻断执行，这是因为如果通过无效的方式使用 `Promise`，并且出现了一个错误阻碍了正常 `Promise` 的构造，结果会得到一个立刻跑出的异常，而不是一个被拒绝的 `Promise`。

然而再举个例子：
```js
let promise = new Promise(() => {
    throw new Error('error')
});
console.log(2333333);
```

这次会正常的打印 `233333`，说明 `Promise` 内部的错误不会影响到 `Promise` 外部的代码，而这种情况我们就通常称为 “吃掉错误”。

其实这并不是 Promise 独有的局限性，try..catch 也是这样，同样会捕获一个异常并简单的吃掉错误。

而正是因为错误被吃掉，Promise 链中的错误很容易被忽略掉，这也是为什么会一般推荐在 Promise 链的最后添加一个 catch 函数，因为对于一个没有错误处理函数的 Promise 链，任何错误都会在链中被传播下去，直到你注册了错误处理函数。

2. **单一值**

Promise 只能有一个完成值或一个拒绝原因，然而在真实使用的时候，往往需要传递多个值，一般做法都是构造一个对象或数组，然后再传递，then 中获得这个值后，又会进行取值赋值的操作，每次封装和解封都无疑让代码变得笨重。

说真的，并没有什么好的方法，建议是使用 ES6 的解构赋值：
```js
Promise.all([Promise.resolve(1), Promise.resolve(2)])
.then(([x, y]) => {
    console.log(x, y);
});
```

3. **无法取消**

Promise 一旦新建它就会立即执行，无法中途取消。

4. **无法得知 pending 状态**

当处于 pending 状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。

### 原生实现 、其一些细节
1. `Promise` 构造器中必须传入函数，否则会抛出错误。

2.` Promise.prototype`上的 `catch(onrejected) `方法是 `then(null,onrejected)` 的别名,并且会处理链之前的任何的`reject`。

3. `Promise.prototype` 上的 `then和 catch` 方法总会返回一个全新的 `Promise` 对象。

4. 如果传入构造器的函数中抛出了错误,该 `promise` 对象的`[[PromiseStatus]]`会赋值为 `rejected`，并且`[[PromiseValue]]`赋值为 `Error` 对象。

5. `then `中的回调如果抛出错误，返回的` promise` 对象的`[[PromiseStatus]]`会赋值为 `rejected`，并且 `[PromiseValue]]`赋值为 `Error` 对象。

6. t`hen` 中的回调返回值会影响 `then` 返回的 `promise` 对象。

```js
// 实现功能： 自动执行 Generator 返回的 Promise 对象， 
//并且将 Promise 的结果在传入到 Generator 进行下一次运行。
class Promise2 {
    constructor(executor){
        const self = this;
        this.status = 'pending'; //等待态
        this.value  = undefined; // 表示当前成功的值
        this.reason = undefined; // 表示是失败的值
        const resolve=(value)=>{ // 成功的方法
            if(self.status === 'pending'){
                self.status = 'resolved';
                self.value = value;
            }
        }
        const reject = (reason)=>{ //失败的方法
            if(self.status === 'pending'){
                self.status = 'rejected';
                self.reason = reason;
            }
        }
        executor(resolve,reject);
    }
     then(onFufiled,onRejected) {
        let timer = null;
        timer = setInterval(()=> {
            if (this.status !== 'pending') {
                if (this.status === 'resolved') {
                    onFufiled(this.value);
                }
                if (this.status === 'rejected') {
                    onRejected(this.reason);
                }
                clearInterval(timer);
            }
        },0)

    }
}
function timeout(ms) {
    return new Promise2((resolve,reject) => {
        setTimeout(resolve, ms);
       /* console.log(111);
        resolve();*/
    });
}

timeout(3000).then(() => {
    console.log('我3秒后被输出');
});
```
参考文章：

[ES6 系列之我们来聊聊 Promise](https://github.com/mqyqingfeng/Blog/issues/98)

[解析 Promise 原理，实现一个Promise](https://segmentfault.com/a/1190000011241512)

[Promise原理与实现](https://www.jianshu.com/p/b4f0425b22a1)

[扒一扒PROMISE的原理，大家不要怕！](https://juejin.im/post/5b66f2935188251afc259f45)

## Generator 自执行函数的实现

通过下面的代码 了解next、yield 是功能是怎么样的，代码的执行过程是如何的。
```javascript
var f = function *(){
  var x = 1;
  var y = yield(x+1);
  var z = yield(x+y);
  return z;
}
var a = f.next();   // 2
var b = f.next(2);  // 3
var c = f.next(4);  // 4
```
第一次执行暂停于`yield(x+1)`,并返回于`x+1`等于2

第二次执行，`next()`的参数`2`,就代替了上面的`yield(x+1)`，所以`y=2`,那么暂停于第二个`yield yield(x+y)`并返回`x+y`等于`3`
同理,第三次执行`z=4，return z`等于`4`

### 单个异步任务

```js
var fetch = require('node-fetch');

function* gen(){
    var url = 'https://api.github.com/users/github';
    var result = yield fetch(url);
    console.log(result.bio);
}
```
为了获得最终的执行结果，需要这样做：
```js
var g = gen();
var result = g.next();

result.value.then(function(data){
    return data.json();
}).then(function(data){
    g.next(data);//这里传入的data 相当于 result
});
```

### 多个异步任务 

只调用了一个接口可以采用上面的那种方式，当我们要调用了多个接口，使用了多个 yield，如果还是采用第一种方式，则会不断的嵌套then. 显而易见的这是不可取的。
```js
var fetch = require('node-fetch');

function* gen() {
    var r1 = yield fetch('https://api.github.com/users/github');
    var r2 = yield fetch('https://api.github.com/users/github/followers');
    var r3 = yield fetch('https://api.github.com/users/github/repos');

    console.log([r1.bio, r2[0].login, r3[0].full_name].join('\n'));
}
```
按照第一种写法：
```js
var g = gen();
var result1 = g.next();

result1.value.then(function(data){
    return data.json();
})
.then(function(data){
    return g.next(data).value;
})
.then(function(data){
    return data.json();
})
.then(function(data){
    return g.next(data).value
})
.then(function(data){
    return data.json();
})
.then(function(data){
    g.next(data)
});
```
显然这不是我们想要的： 可以通过递归的方式来减少then的嵌套
```js
function run(gen) {
    var g = gen();

    function next(data) {
        var result = g.next(data);

        if (result.done) return;

        result.value.then(function(data) {
            return data.json();
        }).then(function(data) {
            next(data);
        });

    }

    next();
}
run(gen);
```
参考文章：[ES6 系列之 Generator 的自动执行](https://github.com/mqyqingfeng/Blog/issues/99)

### 简单原生实现版本co
```js
// 实现功能： 自动执行 Generator 返回的 Promise 对象， 并且将 Promise 的结果在传入到 Generator 进行下一次运行。
function autoRunGen(gen) {
    let result = gen();
    result.next().value.then((data) => {
        return result.next(data).value;
    }).then((data)=> {
        result.next(data)
    })
}
/*参考文章 利用递归*/
function co(gen){
    let result = gen();

    function next(data){
        let result2 = result.next(data);
        if(result2.done) {
            return false;
        }else {
            result2.value.then(function(data) {
                next(data)
            })
        }
    }
    next();

}

function* fetchStepGen(){
    var url = 'https://api.github.com/users/github';
    var result = yield fetch(url);
    var jsonData = yield result.json();
    console.log(jsonData.bio); // "How people build software."
}

autoRunGen(fetchStepGen);
co(fetchStepGen);
```