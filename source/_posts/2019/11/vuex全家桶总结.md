---
title: vuex全家桶总结
date: 2019-11-20 22:10:17
categories: vue
top: 100
tags:
    - vue
    - 学习笔记和总结
photos: 
    - "https://desk-fd.zol-img.com.cn/t_s1440x900c5/g5/M00/0C/00/ChMkJ13VHGeIQbMvAAZiH-yBkAsAAvVdQCGfhkABmI3388.jpg"
---
### vuex全家桶总结

#### Vue脚手架引入store

1. 利用npm包管理工具，进行安装 vuex。在控制命令行中输入下边的命令就可以了。
npm install vuex --save
要注意的是这里一定要加上 –save，因为你这个包我们在生产环境中是要使用的。
2. 一个store文件夹，并在文件夹下新建store.js文件，文件中引入我们的vue和vuex。
```js
import Vue from 'vue';
import Vuex from 'vuex';
```

3、使用我们vuex，引入之后用Vue.use进行引用。
`ue.use(Vuex)`
通过这三步的操作，vuex就算引用成功了，接下来我们就可以尽情的玩耍了。

4、在main.js 中引入新建的vuex文件
`mport store from './store/store'`

5、再然后 , 在实例化 Vue对象时加入 store 对象 :
```js
new Vue({
 el: '#app',
 router,
 store,
 components: { App },
 template: '<App/>'
})
```
#### 基本结构介绍
1. State
+ 单一状态树，保存项目状态唯一数据源
+ 通过 this.$store.state 直接访问
+ mapState 辅助函数

2. Getter
+ 定义 getter 方法
+ 暴露出 store.getters 对象供访问
+ mapGetters 辅助函数

3. Mutation
+ 更改 store 数据
+ 定义 matations 方法，通过 this.$store.commit() 调用
+ Mutation 必须是同步函数
+ mapMutations 辅助函数

4. Action
+ 通过调用 mutation 函数实现更新状态，但不能直接更新状态
+ 可以包含异步操作
+ 通过 this.$store.dispatch() 调用
+ 结合 Promise 使用，实现组合 action
+ mapActions 辅助函数

5. Module
+ 模块化状态，对每个模块添加命名空间
+ 通过辅助函数调用，调用getter,mutation,action方法前添加命名空间

参考文章

[非常全面的vuex----mutation和action的基本使用方法](https://blog.csdn.net/ywl570717586/article/details/80136455)

[VueJS中学习使用Vuex详解](https://segmentfault.com/a/1190000015782272)