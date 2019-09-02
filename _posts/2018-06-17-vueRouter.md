---
layout: post
title: vue router
---


路由简介

休息在家闲着也是闲着，在公司好久没有用vue了，有一些忘记了，写篇博客温故一下。

1、路由：在web开发中，路由是指根据url分配到对应的处理程序。

2、vue-router： 通过管理url实现url和组件的对应和通过url进行组件间的切换。

3、使用的必备三部曲，2/3步都是在`main.js`里面的。

```
  1、安装：npm install vue-router --save
  2、引入：import VueRouter from 'vue-router'
  3、使用：Vue.use(VueRouter )
```

4、正式使用vue-router，需要new一个对象，

```
  const router = new VueRouter({ // 你的配置... })
```

5、注入vue实例中

```
  new Vue({
    el: '#app',
    router
  })
```

如果变量名不为router的话，在注入实例中需要写成router: routerName

6、路由视图渲染：在APP.vue文件中，找到想渲染的位置：<router-view></router-view>

7、router的配置： 配置接受一个routes的字段，值是一个对象数组，是每一个路由的配置。
```
  const router = new VueRouter({
   routes:[
    { path:'/', name:'Home', component: Home }
   ]
  })
```

Home是引入的一个组件，想渲染哪个组件引入哪个：`import Home from '@/components/home.vue'` vue后缀可以不写，@符号代表src路径。

8、当你路由很多的时候，配置都写在`main.js`里面很不方便啊，这个时候可以新建一个单独的js文件作为路由文件，在src目录下，新建一个router的文件夹，里面建一个index.js，名字随意。
文件基本配置，和在main.js里面一样：
```
  import Vue from 'vue'
  import VueRouter from 'vue-router'
  Vue.use(VueRouter)
  import Home from '@/compontents/home'
  let router = new VueRouter({// .... 路由配置})
  // 关键的一步：把配置暴露出去。
  export default router;

  // 然后在main.js文件里面引入
  import router from '@/router/index'
  // 引入成功后注入实例中。
```

9、hash和history模式，默认是hash模式，地址栏’xxx.com/#/????’ ，history模式地址栏：’xxx.com/???’,明显history模式更好一点,history模式可以使用浏览器默认的前进后退功能。
设置：在路由实例中
```
  const router = new VueRouter({
   mode: 'history',
   routes: [
    { path:'/', component: Home }
   ]
  })
```

10、用a标签设置跳转链接的话页面会刷新，所以用vue提供的 router-link 组件。
`<router-link to="/"> home </router-link>`

11、router-link的配置
① 动态插入url： `<router-link :to="url" > home </router-link> `

② 对象形式： `<router-link :to="{path: '/'}" > home </router-link> `

③ 改变默认的标签，router-link 渲染出来默认的是a标签， 可以修改 `<router-link :to="url" tag="div" > home </router-link> `

④ router-link的点击态的样式类名设置： 全局设置的话需要在路由实例里面配置`const router = new VueRouter({ linkActiveClass: 'activeName' })`, 单个router-link配置的话 `<router-link :to="url" tag="div" active-class="className" > home </router-link>` 

⑤ 改变事件触发： `<router-link event="mouseover" > home </router-link>`


12、重定向：
```
  {path: '*', redirect: '/404'}
  redirect: {path: '/home'}
  redirect: {name: 'Home'}
  redirect: to => '/home'
```
