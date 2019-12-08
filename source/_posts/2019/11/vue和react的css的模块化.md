---
title: vue和react的css的模块化
date: 2019-11-17 22:10:17
categories: vue
top: 100
tags:
    - vue
    - 学习笔记和总结
photos: 
    - "https://desk-fd.zol-img.com.cn/t_s1440x900c5/g5/M00/0C/00/ChMkJ13VHGeIQbMvAAZiH-yBkAsAAvVdQCGfhkABmI3388.jpg"
---
<!--more-->
### vue中的css 模块化
#### style scope
在vue的组件中如果<style scope></style>加上了scope，表明该标签里面写的样式都只作用在当前的页面，加上scope后，会自动添加一个唯一的属性，来确定该css样式作用的范围。
比如：
```html
<style scoped>
.example {
  color: red;
}
</style>

<template>
  <div class="example">hi</div>
</template>
```

编译渲染后的结果为：
```html
<style>
.example[data-v-f3f3eg9] {
  color: red;
}
</style>

<template>
  <div class="example" data-v-f3f3eg9>hi</div>
</template>
```
当我们要编写全局样式的时候，或者要覆盖掉当前组件的样式的时候，不添加scope就可以做到。

> 在一个 .vue 文件中可以写多个style 来做到样式的区分开。

```html
<style>
/* global styles */
</style>

<style scoped>
/* local styles */
</style>
```
#### 在vue中使用css modules 
在vue中使用css modules 只需要将scope 换成module `<style module></style>`，同时在css-loader中需要配置

css-loader关于CSS modules的默认配置如下
```
{
  modules: true,
  importLoaders: 1,
  localIdentName: '[hash:base64]'
}
```
可以使用vue-loader的cssModules选项为css-loader进行自定义的配置
```
module: {
  rules: [
    {
      test: '\.vue$',
      loader: 'vue-loader',
      options: {
        cssModules: {
          localIdentName: '[path][name]---[local]---[hash:base64:5]',
          camelCase: true
        }
      }
    }
  ]
}
```
参考文章：
[在vue中使用css modules替代scroped](https://www.cnblogs.com/xiaohuochai/p/8537959.html)