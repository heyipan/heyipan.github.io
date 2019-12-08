---
title: Promise、Generator、Reflect、Map学习笔记
date: 2019-11-14 23:10:17
categories: js
top: 100
tags:
    - js
    - 学习笔记和总结
photos: 
    - "https://desk-fd.zol-img.com.cn/t_s1440x900c5/g5/M00/0C/00/ChMkJ13VHGeIQbMvAAZiH-yBkAsAAvVdQCGfhkABmI3388.jpg"
---
<!--more-->
## 11-13 学习总结和笔记

### 了解

1. Promise。

2. Generator、Reflect、Map。
<!--more-->
# Promise、Generator、Reflect、Map学习笔记

## Promise

Promise的出现是为了解决回调地狱的，使用Promise，我们就可以利用then进行「链式回调」

构建Promise

```javascript
//构建Promise
var promise = new Promise(function (resolve, reject) {
    if (/* 异步操作成功 */) {
        resolve(data);
    } else {
        /* 异步操作失败 */
        reject(error);
    }
});
```
类似构建对象，使用new来构建一个Promise。Promise接受一个「函数」作为参数，该函数的两个参数分别是resolve和reject。这两个函数就是就是「回调函数」，由JavaScript引擎提供。

Promise实例生成以后，可以用then方法指定resolved状态和reject状态的回调函数。

```javascript
//promise.then(onFulfilled, onRejected);

promise.then(function(data) {
  // do something when success
}, function(error) {
  // do something when failure
});
```
then方法会返回一个Promise。它有两个参数，分别为Promise从pending变为fulfilled和rejected时的回调函数（第二个参数非必选）。这两个函数都接受Promise对象传出的值作为参数。

### .catch()  

该方法是.then(undefined, onRejected)的别名，用于指定发生错误时的回调函数。

```javascript
promise.then(function(data) {
    console.log('success');
}).catch(function(error) {
    console.log('error', error);
});

/*******等同于*******/
promise.then(function(data) {
    console.log('success');
}).then(undefined, function(error) {
    console.log('error', error);
});
```

`promise`对象的错误，会一直向后传递，直到被捕获。即错误总会被下一个catch所捕获。then方法指定的回调函数，若抛出错误，也会被下一个`catch`捕获。`catch`中也能抛错，则需要后面的`catch`来捕获。

```javascript
sendRequest('test.html').then(function(data1) {
    //do something
}).then(function (data2) {
    //do something
}).catch(function (error) {
    //处理前面三个Promise产生的错误
});
```

`promise`一旦`resolve`了再抛错，也不会变为`rejected`，就不会被`catch`了。

```javascript
var promise = new Promise(function(resolve, reject) {
  resolve();
  throw 'error';
});

promise.catch(function(e) {
   console.log(e);      //This is never called
});
```
> 如果没有使用catch方法指定处理错误的回调函数，Promise对象抛出的错误不会传递到外层代码，即不会有任何反应

### .all()

该方法用于将多个Promise实例，包装成一个新的Promise实例。 `var p = Promise.all([p1, p2, p3]);`

Promise.all方法接受一个数组（或具有Iterator接口）作参数，数组中的对象（p1、p2、p3）均为promise实例

（如果不是一个promise，该项会被用Promise.resolve转换为一个promise)。它的状态由这三个promise实例决定。

    1. 当p1, p2, p3状态都变为fulfilled，p的状态才会变为fulfilled，并将三个promise返回的结果，按参数

    的顺序（而不是 resolved的顺序）存入数组，传给p的回调函数

    2. 当p1, p2, p3其中之一状态变为rejected，p的状态也会变为rejected，并把第一个被reject的promise的

    返回值，传给p的回调函数，
```javascript
var p1 = new Promise(function (resolve, reject) {
    setTimeout(resolve, 3000, "first");
});
var p2 = new Promise(function (resolve, reject) {
    resolve('second');
});
var p3 = new Promise((resolve, reject) => {
  setTimeout(resolve, 1000, "third");
}); 

Promise.all([p1, p2, p3]).then(function(values) { 
  console.log(values); 
});

-------output-------
//约 3s 后
["first", "second", "third"] 


var p1 = new Promise((resolve, reject) => { 
  setTimeout(resolve, 1000, "one"); 
}); 
var p2 = new Promise((resolve, reject) => { 
  setTimeout(reject, 2000, "two"); 
});
var p3 = new Promise((resolve, reject) => {
  reject("three");
});

Promise.all([p1, p2, p3]).then(function (value) {
    console.log('resolve', value);
}, function (error) {
    console.log('reject', error);    // => reject three
});

-------output-------
reject three
```
> 这多个 promise 是同时开始、并行执行的，而不是顺序执行

### .race()

该方法同样是将多个Promise实例，包装成一个新的Promise实例。`var p = Promise.race([p1, p2, p3]);`

`Promise.race`方法同样接受一个数组（或具有`Iterator`接口）作参数。当`p1, p2, p3`中有一个实例的状态发生改变（变为f`ulfilled或rejected`），`p`的状态就跟着改变。并把第一个改变状态的`promise`的返回值，传给p的回调函数。

```javascript
var fastPromise = new Promise(function (resolve) {
    setTimeout(function () {
        console.log('fastPromise');
        resolve('resolve fastPromise');
    }, 100);
});
var slowPromise = new Promise(function (resolve) {
    setTimeout(function () {
        console.log('slowPromise');
        resolve('resolve slowPromise');
    }, 1000);
});
// 第一个promise变为resolve后程序停止
Promise.race([fastPromise, slowPromise]).then(function (value) {
    console.log(value);    // => resolve fastPromise
});
-------output-------
fastPromise
resolve fastPromise
slowPromise     //仍会执行
```

### .resolve()

它可以看做new Promise()的快捷方式

```javascript
Promise.resolve('Success');

/*******等同于*******/
new Promise(function (resolve) {
    resolve('Success');
});
```
## Generator

### 它的语法规则

Generator 函数是一个普通函数，但是有两个特征。一是，function关键字与函数名之间有一个星号；

二是，函数体内部使用yield表达式，定义不同的内部状态（yield在英语里的意思就是“产出”）。

简单实现

```javascript
function* helloWorldGenerator() {
  yield 'hello';
  yield 'world';
  return 'ending';
}

var hw = helloWorldGenerator();
```
上面函数它内部有两个yield表达式（hello和world），即该函数有三个状态：hello，world 和 return 语句（结束执行）。

然后，Generator 函数的调用方法与普通函数一样，也是在函数名后面加上一对圆括号。不同的是，调用 

Generator 函数后，该函数并不执行，返回的也不是函数运行结果，而是一个遍历器对象（Iterator Object）。

```javascript
hw.next()
// { value: 'hello', done: false }

hw.next()
// { value: 'world', done: false }

hw.next()
// { value: 'ending', done: true }

hw.next()
// { value: undefined, done: true }
```
ES6 没有规定，function关键字与函数名之间的星号，写在哪个位置。这导致下面的写法都能通过。

```javascript
function * foo(x, y) { ··· }
function *foo(x, y) { ··· }
function* foo(x, y) { ··· }
function*foo(x, y) { ··· }
```
由于 Generator 函数仍然是普通函数，所以一般的写法是上面的第三种，即星号紧跟在function关键字后面。

### yield表达式

+ yield是ES6的新关键字，使生成器函数执行暂停，yield关键字后面的表达式的值返回给生成器的调用者。它可以被认为是一个基于生成器的版本的return关键字。

+ yield关键字实际返回一个IteratorResult（迭代器）对象，它有两个属性，value和done，分别代表返回值和是否完成。

+ yield无法单独工作，需要配合generator(生成器)的其他函数，如next，懒汉式操作，展现强大的主动控制特性。

而且它只能用在遍历器函数里面,并且它有一个返回值{value: xxx,done: false},value就是当前遍历器暂停时返回

的结果，done为false得时候，表示遍历器没遍历完，为true表示遍历已结束。

```javascript
function *foo(){
  var x = 1;
  y = yield(x+1);
  return;
}
var f = foo();
```
遍历器函数一个重要的特点就是需要next()方法才能执行，所以上面f = foo()什么都没发生，要用`f.next();`

`f.next()`是遍历器第一次执行，当遍历至关键字`yield`时，函数暂停，并返回yield后面的值，所以此时返回`{value: 2,done: false}`

再执行一次`f.next()`,那么遍历器函数则从上次暂停的`yield`处开始，直接到`return`语句，所以结果是`{value: undefined,done: true}`

next()可以接收参数，就是可以将传入的参数作用于上一次yield

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

> 注意: next()可以接受一个函数，将函数执行的结果返回 作为其值


## Reflect 
### 为什么要设计Reflect？

（1）将Object对象的属于语言内部的方法放到Reflect对象上，即从Reflect对象上拿Object对象内部方法。
（2）将用 老Object方法 报错的情况，改为返回false

老写法
```js
try {
  Object.defineProperty(target, property, attributes);
  // success
} catch (e) {
  // failure
}
```
新写法
```js
if (Reflect.defineProperty(target, property, attributes)) {
  // success
} else {
  // failure
}
```
（3）让Object操作变成函数行为

老写法（命令式写法）

`'name' in Object //true`

新写法

`Reflect.has(Object,'name') //true`

（4）Reflect与Proxy是相辅相成的，在Proxy上有的方法，在Reflect就一定有

### Reflect的常用API

1. Reflect.get(target,property,receiver)

与从 对象 (target[propertyKey]) 中读取属性类似，但它是通过一个函数执行来操作的。

`target`为需要取值的目标对象，`propertyKey`需要获取的值的键值，`receiver`为如果`target`对象中指定了`getter`，`receiver`则为`getter`调用时的`this`值。

> 注意：如果Reflect.get()的第一个参数不是对象，则会报错。

```js 
// Object
var obj = { x: 1, y: 2 };
Reflect.get(obj, "x"); // 1

// Array
Reflect.get(["zero", "one"], 1); // "one"

// Proxy with a get handler
var x = {p: 1};
var obj = new Proxy(x, {
  get(t, k, r) { return k + "bar"; }
});
Reflect.get(obj, "foo"); // "foobar"
```
2. Reflect.set(target,propName,propValue,receiver)

就像在一个对象上设置一个属性。

`target`:需要取值的目标对象,`propertyKey`:需要获取的值的键值,`receiver`:如果`target`对象中指定了`getter`，`receiver`则为`getter`调用时的`this`值。

> 如果目标值类型不是 Object，则抛出一个 TypeError。

```js
// Object
var obj = {};
Reflect.set(obj, "prop", "value"); // true
obj.prop; // "value"

// Array
var arr = ["duck", "duck", "duck"];
Reflect.set(arr, 2, "goose"); // true
arr[2]; // "goose"

// It can truncate an array.
Reflect.set(arr, "length", 1); // true
arr; // ["duck"];

// With just one argument, propertyKey and value are "undefined".
var obj = {};
Reflect.set(obj); // true
Reflect.getOwnPropertyDescriptor(obj, "undefined");
// { value: undefined, writable: true, enumerable: true, configurable: true }
```
3. Reflect.has()

作用与 in 操作符 相同。

`target`目标对象. `propertyKey`:属性名，需要检查目标对象是否存在此属性。
> 如果目标对象并非Object 类型，抛出TypeError。
```js
var obj= {
  name: "chen",
};
```
老写法 `'name' in obj // true`

新写法`Reflect.has(obj, 'name') // true`

参考文章：

[ES6之Reflect](https://www.jianshu.com/p/4a5eca0536c3)

[《深入理解ES6》笔记——代理（Proxy）和反射（Reflection）API（12）](https://segmentfault.com/a/1190000010471230)

[ES6黑科技实践--proxy,reflect](https://blog.csdn.net/u013707249/article/details/78842602)

## Map

javascript的Object本身就是键值对的数据结构，但实际上属性和值构成的是”字符串-值“对，属性只能是字符串，如果传个对象字面量作为属性名，那么会默认把对象转换成字符串，结果这个属性名就变成”[object Object]“。

ES6提供了”值-值“对的数据结构，键名不仅可以是字符串，也可以是对象。它是一个更完善的Hash结构。

1. 键值对，键可以是对象。
```js
const map1 = new Map()
const objkey = {p1: 'v1'}

map1.set(objkey, 'hello')
console.log(map1.get(objkey))
```
结果：`hello`
 

2. Map可以接受数组作为参数，数组成员还是一个数组，其中有两个元素，一个表示键一个表示值。
```js
const map2 = new Map([
  ['name', 'Aissen'],
  ['age', 12]
])
console.log(map2.get('name'))
console.log(map2.get('age'))
```      
结果：
```js
Aissen
12
```
3. size  获取map的大小。

4. get 获取键对应的值。

5. has 判断指定的键是否存在。

6. delete  删除键值对。

7. clear 删除map中的所有键值对

8. set 设置键值对，键可以是各种类型，包括undefined，function。

```js 
const map4 = new Map();
map4.set('k1', 6)        // 键是字符串
map4.set(222, '哈哈哈')     // 键是数值
map4.set(undefined, 'gagaga')    // 键是 undefined

const fun = function() {console.log('hello');}
map4.set(fun, 'fun') // 键是 function

console.log('map4 size: %s', map4.size)
console.log('undefined value: %s', map4.get(undefined))
console.log('fun value: %s', map4.get(fun))
```
结果：
```js 
map4 size: 4
undefined value: gagaga
fun value: fun
```
也可对set进行链式调用。
```js
map4.set('k2', 2).set('k3', 4).set('k4', 5)
console.log('map4 size: %s', map4.size)
```
结果：`map4 size: 7`

参考文章
[ES6，新增数据结构Map的用法](https://www.cnblogs.com/kongxianghai/p/7309735.html)


