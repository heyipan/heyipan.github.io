---
title: Decorator装饰器 学习笔记
date: 2019-11-22 23:10:17
categories: js
top: 100
tags:
    - js
    - 学习笔记和总结
photos: 
    - "https://desk-fd.zol-img.com.cn/t_s1024x768c5/g5/M00/0C/02/ChMkJ13V9q6IOSp_AAPNPlvQOwwAAvV_QJYgfoAA81W947.jpg"
---

## 装饰器分类
Decorator装饰器是一种函数，写成@ + 函数名。它可以放在类和类方法的定义前面。
```js
@frozen 
class Foo {
  @configurable(false)
  @enumerable(true)
  method() {}

  @throttle(500)
  expensiveMethod() {}
}
```
上面代码一共使用了四个装饰器，一个用在类本身，另外三个用在类方法。所以个人理解：
装饰器分为**类装饰器**和**类属性**装饰器

### 类装饰器
顾名思义是用来装饰整个类
```
@testable
class MyTestableClass {
  // ...
}

function testable(target) {
  target.isTestable = true;
}

MyTestableClass.isTestable // true
```
上面代码中，`@testable`就是一个装饰器。它修改了`MyTestableClass`这个类的行为，为它加上了静态属性`isTestable`。`testable`函数的参数`target`是`MyTestableClass`类本身。

基本上，装饰器的行为就是下面这样。

```
@decorator
class A {}

// 等同于

class A {}
A = decorator(A) || A;
```
也就是说，装饰器是一个对类进行处理的函数。装饰器函数的第一个参数，就是所要装饰的目标类。

```
function testable(target) {
  // ...
}
```
`testable`函数的参数`target`，就是会被装饰的类。

如果觉得一个参数不够用，可以在装饰器外面再封装一层函数。
```
function testable(isTestable) {
  return function(target) {
    target.isTestable = isTestable;
  }
}

@testable(true)
class MyTestableClass {}
MyTestableClass.isTestable // true

@testable(false)
class MyClass {}
MyClass.isTestable // false
```
装饰器testable可以接受参数，这就等于可以修改装饰器的行为。

> 注意，装饰器对类的行为的改变，是代码编译时发生的，而不是在运行时。这意味着，装饰器能在编译阶段运行代码。也就是说，装饰器本质就是编译时执行的函数。

### 类属性装饰器
装饰器不仅可以装饰类，还可以装饰类的属性
```
class Person {
  @readonly
  age=18;
  @readonly
  name() { return `${this.first} ${this.last}` }
}

```
装饰器readonly用来装饰“类”的name方法和age属性。

实现readonly装饰器
```
function readonly(target, name, descriptor){
  // descriptor对象原来的值如下
  // {
  //   value: specifiedFunction, //对象属性的默认值，默认值为undefined
  //   enumerable: false,//对象属性是否可通过for-in循环，flase为不可循环，默认值为true
  //   configurable: true,//能否使用delete、能否需改属性特性、或能否修改访问器属性、，false为不可重新定义，默认值为true
  //   writable: true,//对象属性是否可修改,flase为不可修改，默认值为true
  // };
  descriptor.writable = false;
  return descriptor;
}

readonly(Person.prototype, 'name', descriptor);
// 类似于
Object.defineProperty(Person.prototype, 'name', descriptor);

```
类属性装饰器参数：

- 第一个参数是类的**原型对象**，上例是`Person.prototype`，装饰器的本意是要“装饰”类的实例，但是这个时候实例还没生成，所以只能去装饰原型（这不同于类的装饰，那种情况时`target`参数指的是类本身）；
- 第二个参数是所要装饰的属性名，
- 第三个参数是该属性的描述对象。

上面代码说明，装饰器（readonly）会修改属性的描述对象（descriptor），然后被修改的描述对象再用来定义属性。


### 装饰器执行顺序
如果同一个方法有多个装饰器，会像剥洋葱一样，先从外到内进入，然后由内向外执行。
```js
function dec(id){
  console.log('evaluated', id);
  return (target, property, descriptor) => console.log('executed', id);
}

class Example {
    @dec(1)
    @dec1(2)
    method(){}
}
// evaluated 1
// evaluated 2
// executed 2
// executed 1
```
外层装饰器@dec(1)先进入，但是内层装饰器@dec(2)先执行。

### 练习

#### 练习1 调用这个类的所有方法的时候  都要输出日志
```

function log(terget){
    const desc = Object.getOwnPropertyDescriptors(terget.prototype);
    
    for (const key of Object.key(desc) ) {
        if(key === 'contstructor') {
            continue;
        }
        
        const func = desc[key].value;
        if(typeof func === 'function'){
            Object.defineProperty(terget.prototype,key,{
                value(...args){
                    console.log('before'+key);
                    const ret = func.apply(this,args);
                    console.log('after'+key);
                    
                    return ret;
                }
            })
        }
    }
}
@log
class Numberic(){
    PI = 3.14
    
    add(...nums){
        return nums.reduce((p,n) => (p+n),0)
        
    }
}
```
#### 练习2 对一个类的方法进行参数的验证，同时设置一个类的属性只能是只读。
```
function readOnly(terget,name,descriptor){
    descriptor.writable = false;
}

function validate(terget,name,descriptor){
    const func = descriptor.value;
    descriptor.value = function(...args){
        for(let num of args){
            if(typeOf num !== 'number'){
                return new Error(`${num} is not a number `)
            }
        }
        
        return func.apply(...args);
    }
}

class Numberic(){
    @readOnly
    PI = 3.14
    
    @validate
    add(...nums){
        return nums.reduce((p,n) => (p+n),0)
        
    }
}

```
