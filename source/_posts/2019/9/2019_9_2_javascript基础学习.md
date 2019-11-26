---
title: javascript基础学习
date: 2019-9-2 23:10:17
categories: js
top: 100
tags:
    - js
    - 学习笔记和总结
photos: 
    - "https://desk-fd.zol-img.com.cn/t_s1280x1024c5/g2/M00/0F/07/ChMlWl1nOc2IbvfQAAZNLsuXWlkAAM3EQN7GXAABk1G366.jpg"
---
## 7-04 学习总结和笔记

### 了解

1. 了解学习计划。

2. 与老师探讨学习策略，包括学习内容以及学习方法。

3. 探讨以往培训生学习经验，总结新的学习方案。
<!--more-->


### 调试

#### chrome开发工具的使用

1. 安装google访问助手插件，建议多用google查找相关问题和资料。 了解翻墙的原理，简单的来讲就是通过访问香港、澳门、台湾的待在的服务器，然后该服务区在通过再去访问国外的服务器，从而实现翻墙操作。个人感觉于服务器代理的原理差不多。

2. elements调样式，Console拿到实例，Network看请求头URL请求方式响应码，请求参数，Sources定位源码，Performance优化，显示渲染时间，Application缓存。

> 注：请求头中常用字段：Authorization，请求体：参数。


### js基础学习

发展历史，基础知识，常见字符串、数组方法，调试技巧

上下文、闭包、this、apply、call

原型、原型链继承、项目中的应用

结合ajax、上传demo讲解、同步异步、post表单类型等


#### 变量提升和作用域链

1. 函数作用域和声明提前：JavaScript 的函数作用域是指在函数内声明的所有变量在函数体内始终是可见的，这意味着变量在声明之前甚至已经可用。JavaScript 的这个特性被非正式地称为声明提前（hoisting），即 JavaScript 函数里声明的所有变量（但不涉及赋值）都被提前至函数体的顶部

```javascript
    var scope = "global";
    function f() {
        console.log(scope);  // 输出"undefined"，而不是"global"
        var scope = "local"; // 变量在这里赋初始值，但变量本身在函数体内任何地方均是有定义的
        console.log(scope);  // 输出"local"
    }

    function f() {
        var scope;          // 在函数顶部声明了局部变量
        console.log(scope); // 变量存在，但其值是"undefined"
        scope = "local";    // 这里将其初始化并赋值
        console.log(scope); // 这里它具有了我们所期望的值
    }
```

2. 作用域链：当代码在一个环境中执行时，会创建变量对象的一个作用域链，作用域链的前端，始终都是当前执行的代码所在环境的变量对象。作用域链中的下一个变量对象来自包含（外部）环境，而再下一个变量对象则来自下一个包含环境。这样，一直延续到全局执行环境；全局执行环境的变量对象始终都是作用域链中的最后一个对象。

```javascript
    var color = "blue";

    function changeColor(){
        var anotherColor = "red";

        function swapColors(){
            var tempColor = anotherColor;
            anotherColor = color;
            color = tempColor;

            // 这里可以访问color、anotherColor和tempColor
        }

    // 这里可以访问color和anotherColor，但不能访问tempColor
    swapColors();
}

```

#### 词法作用域

静态作用域与动态作用域

    词法作用域：词法作用域是指在词法分析阶段就确定了，不会改变。变量的作用域是在定义时决定而不是执行
    时决定，也就是说词法作用域取决于源码，通过静态分析就能确定，因此词法作用域也叫做静态作用域。

    动态作用域：动态作用域是在运行时根据程序的流程信息来动态确定的，而不是在写代码时进行静态确定的。 
    动态作用域并不关心函数和作用域是如何声明以及在何处声明的，只关心它们在何处调用。参考this

JavaScript的词法作用域是静态作用域

如果一个文档流中包含多个script代码段（用script标签分隔的js代码或引入的js文件），它们的运行顺序是：

1. 读入第一个代码段（js执行引擎并非一行一行地分析程序，而是一段一段地分析执行的）

2. 做词法分析，有错则报语法错误（比如括号不匹配等），并跳转到步骤5

3. 对var变量和function定义做“预解析“（永远不会报错的，因为只解析正确的声明）

4. 执行代码段，有错则报错（比如变量未定义）

5. 如果还有下一个代码段，则读入下一个代码段，重复步骤2

6. 完成

理解js词法作用域
```javascript
    var value = 1;
    function foo() {
    console.log(value);
    }
    function bar() {
    var value = 2;
    foo();
    }
    bar();//1
```
假设JavaScript采用静态作用域，分析执行过程：

  执行 foo 函数，先从 foo 函数局部作用域中查找是否有变量 value，如果没有，就从全局作用域中查找变量value的值，所以结果会打印 1。

假设JavaScript采用动态作用域，让我们分析下执行过程：

  执行 foo 函数，依然是从 foo 函数内部查找是否有局部变量 value。如果没有，就从调用函数的作用域，也就是 bar 函数内部查找 value 变量，所以结果会打印 2。

JavaScript采用的是静态作用域，所以这个例子的结果是 1。

参考链接 [JavaScript作用域、上下文、执行期上下文、作用域链、闭包](https://blog.csdn.net/qq_27626333/article/details/78463565)

#### 原型链

1. 每一个对象都有__proto__属性
2. 只有函数对象有prototyp属性

![](http://image.bubuko.com/info/201601/20180110174710584431.jpg)

#### 字符串操作

slice(start,end)

subString(start,end) start大于end；开会自动转化

substr(start,end)

concat（）返回字符串，链接字符串

split（）

replace（）去掉空格