---
title: vue基础学习笔记(sass、router、传参方式、keep-Alive  actived和deactived)
date: 2019-9-5 23:10:17
categories: vue
top: 100
tags:
    - vue
photos: 
    - "https://desk-fd.zol-img.com.cn/t_s1680x1050c5/g5/M00/0C/00/ChMkJ13VHF6IdDshACMTOWET5fgAAvVdAFSmTgAIxNR699.jpg"
---

>  @ 指定到src目录
在vue项目中 import 导入文件的时候，@是webpack指定了的别名，默认是指向src目录下。

### vue项目配置sass和less

基于前面创建的工程(用脚手架2.0版本创建的

1. 安装sass的依赖包
```js
npm install --save-dev sass-loader 
//sass-loader依赖于node-sass 
npm install --save-dev node-sass
```

2. 在build文件夹下的webpack.base.conf.js的rules里面添加配置
```js
{ test: /\.sass$/, loaders: [‘style’, ‘css’, ‘sass’] }
```

3. 最后，在代码中的 style 标签中 加上`lang="less"`属性即可
`<style type="text/less" lang=”less" rel="stylesheet/less" scoped>`

### vue 配置router
1. 在路由配置页面引入路由组件
```js 
import Vue from 'vue'
import Router from 'vue-router'
```
2. 然后在该页面配置路由信息
```js
import Vue from 'vue'
import Router from 'vue-router'
import Tab from '@/views/tab'
import Contact from '@/views/contact'
import Setting from '@/views/setting'
import Log from '@/views/log'
import One from '@/views/setting/children/one'
import Two from '@/views/setting/children/two'

Vue.use(Router)

export default new Router({
  routes: [
    {
      path: '/tab',
      component: Tab,
      children: [
        {
          path: '/tab/contact', component: Contact
        },
        {
          path: '/tab/setting',
          component: Setting,
          children: [
            {
              path: '/tab/setting/one', component: One, name: 'setting1'
            },
            {
              path: '/tab/setting/two', component: Two, name: 'setting2'
            }
          ]
        },
      ]
    },
    {
      path: '/log',
      name: 'log',
      component: Log
    }
  ]
})
```

3. 在路由页面引入页面的两种方式的import和require

我们看到是用的require的方式写的路由，所以上面的import就注释掉了，这种写法的好处，不仅仅是简单，还有这样写是按需加载，访问此路由时才加载这个js，会避免进入首页时加载内容过多，因为import引入，当项目打包时路由里的所有component都会打包在一个js中，而用require会将你的component分别打包成不同的js

```js
//上面是import的方式。
import Vue from 'vue'
import Router from 'vue-router'

Vue.use(Router)

const A = r => require.ensure([],() => r(require('@/views/RouterTest/A.vue')),'A');
const B = r => require.ensure([],() => r(require('@/views/RouterTest/B.vue')),'B');

export default new Router({
    routes: [
    {path:'/a',name:'A',component:A},
    {path:'/b',name:'B',component:B},
    ]
})
```

#### vue 路由传参 params 与 query两种方式的区别

query更加类似于我们ajax中get传参，params则类似于post，前者在浏览器地址栏中显示参数，后者则不显示
在页面通过 this.$router.query和this.$router.params获取对应的参数。

由于params是类似于post传参 所以当页面刷新后，就不能在获取到路由中的参数了。

比如:下面这个例子
```js
//路由关系
export default new Router({
  routes: [
    {
      path: '/tab',
      component: Tab,
      children: [
        {
          path: '/tab/contact', component: Contact
        },
        {
          path: '/tab/setting',
          component: Setting,
          children: [
            {
              path: '/tab/setting/one', component: One, name: 'setting1'
            },
            {
              path: '/tab/setting/two', component: Two, name: 'setting2'
            }
          ]
        },
      ]
    },
    {
      path: '/log',
      name: 'log',
      component: Log
    }
  ]
})
```
主要路由跳转页面

```js
//selete.js
<template>
  <div class="setting">
    Setting

    <br>
    <button @click="toOne"> to one</button>

    <br>

    <button @click="toTwo"> to two</button>
    <keep-alive>
      <router-view/>
    </keep-alive>
  </div>
</template>

<script>
  export default {
    name: "setting",
    methods: {
      toOne() {
        this.$router.push({
          name: 'setting1', query: {
            msg: 'aa'
          }
        })
      },
      toTwo() {
        this.$router.push({
          name: 'setting2', params: {
            msg: 'bb'
          }
        })
      }
    }
  }
</script>
```
one.js
```js
<template>
  <div class="setting-one">
    Setting One

    <button @click="toLog">to Log</button>
  </div>
</template>

<script>
  export default {
    name: "one",
    data() {
      return {
        msg1: ''
      }
    },
    created() {
      console.log('one created')
    },
    activated() {
      console.log('one activated')
    },
    deactivated() {
      console.log('one deactivated')
    },
    mounted() {
      const { msg } = this.$route.query
      this.msg1 = msg
      console.log('this.msg1 = ', this.msg1)
    },
    methods: {
      toLog() {
        this.$router.push({
          name: 'log'
        })
      }
    },
    destroyed() {
      console.log('one destroyed')
    }
  }
</script>
```

two.js
```js
<template>
  <div class="setting-two">
    Setting Two

    <button @click="toLog">to Log</button>
  </div>
</template>

<script>
  export default {
    name: "two",
    data() {
      return {
        msg2: ''
      }
    },
    created() {
      console.log('two created')
    },
    mounted() {
      const { msg } = this.$route.params
      this.msg2 = msg
      console.log('this.msg2 = ', this.msg2)
    },
    methods: {
      toLog() {
        this.$router.push({
          name: 'log'
        })
      }
    },
    destroyed() {
      console.log('two destroyed')
    }
  }
</script>
```

在`select.js`中两个点击事件可以控制路由的切换，同时两个路由切换的传参方式也不同。当点击`toOne()`的时候，我们发现`ne.js`中的`onted`方法执行了，并且可以得到参数，对应的浏览器地址栏中也显示参数。当刷新页面的时候，还是可以得到参数。

当点击`toTwo()`的时候，我们发现`wo.js`中的`monted`方法执行了，并且可以得到参数，对应的浏览器地址栏中没有显示参数。当刷新页面的时候，打印参数值为`undefined`。

同时在路由进行切换的时候，我们发现都执行了`destoryed`方法，下次再切换到当前路由的时候`created()`又会在从新执行。所以这里存在一个小的问题，当我们第一次进入该页面的时候，发送了数据请求。当离开该页面，切换到下一个页面在返回回来的时候，又会从新发送数据请求。这样会造成请求次数的浪费，而且用户的体验也不好。所以采用在`<router-view/>`外面使用`keep-Alive`标签。

#### keep-Alive  actived和deactived的用法。

在上面的select.js 中我们通过将kee-Alice包裹起来，然后进行路由跳转的时候发现，切换路由的时候，组件的destoryed方法不再执行，同时当切换回去的时候，create也不再执行，这样就做到了页面不再渲染的效果。

但是我们的需求就是每次进入页面的时候都要进行请求呢。  keep-Alive 提供了两个方法来代替销毁和创造两个函数，actived和deactived。前一个方法会在进入的页面的时候执行，而后者会在离开的时候执行
