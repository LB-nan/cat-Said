---
layout: post
title: 前端面试题之vue
categories: 面试题
description: 面试题,vue
keywords: 面试题, vue, 双向绑定, 依赖收集, 生命周期
---

vue面试常问知识点汇总

### 1、vue的原理

当一个Vue实例创建时，Vue会遍历data中的属性，用 Object.defineProperty（vue3.0使用proxy ）将它们转为 getter/setter，并且在内部追踪相关依赖，在属性被访问和修改时通知变化。 每个组件实例都有相应的 watcher 程序实例，它会在组件渲染的过程中把属性记录为依赖，之后当依赖项的setter被调用时，会通知watcher重新计算，从而致使它关联的组件得以更新。

### 2、双向绑定

Vue.js 是采用**数据劫持**结合**发布者-订阅者模式**的方式，通过Object.defineProperty()来劫持各个属性的setter，getter，在数据变动时发布消息给订阅者，触发相应的监听回调。

数据劫持：

- 在vue2.x中使用Object.defineProperty()进行的数据劫持，缺点是对于数组而言，大部分操作是拦截不到的，只是vue在内部重写了数组的函数解决了这个问题
- 在vue3.x中使用了proxy进行数据劫持，解决了2.x拦截不到数组的问题，缺点是兼容性问题，因为proxy是ES6的标准

### 3、生命周期

1. beforeCreate(创建前)：数据观测和初始化事件还未开始，此时 data 的响应式追踪、event/watcher 都还没有被设置，也就是说不能访问到data、computed、watch、methods上的方法和数据。
2. created(创建后)：实例创建完成，实例上配置的 options 包括 data、computed、watch、methods 等都配置完成，但是此时渲染得节点还未挂载到 DOM，所以不能访问到 `$el` 属性。
3. beforeMount(挂载前)：在挂载开始之前被调用，相关的render函数首次被调用。实例已完成以下的配置：编译模板，把data里面的数据和模板生成html。此时还没有挂载html到页面上。
4. mounted(挂载后)：在el被新创建的 vm.$el 替换，并挂载到实例上去之后调用。实例已完成以下的配置：用上面编译好的html内容替换el属性指向的DOM对象。完成模板中的html渲染到html 页面中。此过程中进行ajax交互。
5. beforeUpdate(更新前)：响应式数据更新时调用，此时虽然响应式数据更新了，但是对应的真实 DOM 还没有被渲染。
6. updated(更新后)：
7. 在由于数据更改导致的虚拟DOM重新渲染和打补丁之后调用。此时 DOM 已经根据响应式数据的变化更新了。调用时，组件 DOM已经更新，所以可以执行依赖于DOM的操作。然而在大多数情况下，应该避免在此期间更改状态，因为这可能会导致更新无限循环。该钩子在服务器端渲染期间不被调用。
8. beforeDestory(销毁前)：实例销毁之前调用。这一步，实例仍然完全可用，`this` 仍能获取到实例。
9. destroyed(销毁后)：实例销毁后调用，调用后，Vue 实例指示的所有东西都会解绑定，所有的事件监听器会被移除，所有的子实例也会被销毁。该钩子在服务端渲染期间不被调用。

另外还有 `keep-alive` 独有的生命周期，分别为 `activated` 和 `deactivated` 。用 `keep-alive` 包裹的组件在切换时不会进行销毁，而是缓存到内存中并执行 `deactivated` 钩子函数，命中缓存渲染后会执行 `activated` 钩子函数。

### 4、常见的事件修饰符

1. .stop：等同于 JavaScript 中的 `event.stopPropagation()` ，防止事件冒泡
2. .prevent：等同于 JavaScript 中的 `event.preventDefault()` ，防止执行预设的行为（如果事件可取消，则取消该事件，而不停止事件的进一步传播）
3. .capture：与事件冒泡的方向相反，事件捕获由外到内
4. .self：只会触发自己范围内的事件，不包含子元素
5. .once：只会触发一次

### 5、vue和react的区别

相同处：

1. 都将注意力集中保持在核心库，而将其他功能如路由和全局状态管理交给相关的库
2. 都有自己的构建工具，能让你得到一个根据最佳实践设置的项目模板
3. 都使用了VDOM，提供重绘性能
4. 都有props的概念，允许组件间传递数据
5. 都鼓励组件化应用

不同之处：

1. 数据处理：Vue默认支持数据双向绑定，而React一直提倡单向数据流
2. 虚拟DOM：Vue宣称可以更快地计算出Virtual DOM的差异，这是由于它在渲染过程中，会跟踪每一个组件的依赖关系，不需要重新渲染整个组件树。React会全部子组件重新渲染
3. 组件化：Vue鼓励写近似常规HTML的模板。写起来很接近标准 HTML元素，只是多了一些属性。React推荐你所有的模板通用JavaScript的语法扩展——JSX书写。
4. 监听数据的原理：vue使用getter/setter以及一些函数的劫持，React使用比较引用的方法，react这种可能会造成大量不必要的vdom的渲染

### 6、vue的优点

1. 轻量级、易学
2. 双向数据绑定
3. 组件化
4. 虚拟DOM节省了大量DOM操作
5. 运行速度更快

### 7、template和jsx的有什么分别

- template更加简单、直观、更好维护，但是不够灵活
- jsx更灵活，复杂的组件里更有优势

### 8、组件通信

父组件通过`props`向子组件传递数据，子组件通过`$emit`和父组件通信

父级=》子级

```js
<template>
    <div id="father">
        <son :msg="msgData" :fn="myFunction"></son>
    </div>
</template>

// 子级
<template>
    <div id="son">
        <p>{{msg}}</p>
        <button @click="fn">按钮</button>
    </div>
</template>
<script>
export default {
    name: "son",
    props: ["msg", "fn"]
};
```

子级=》父级

```js
// 父级
methods: {
  onEmitIndex(idx) {
    this.currentIndex = idx
  }
}


// 子级
 methods: {
   emitIndex(index) {
     this.$emit('onEmitIndex', index) // 触发父组件的方法，并传递参数index
   }
 }
```

### 9、eventBus事件总线

`eventBus`事件总线适用于**父子组件**、**非父子组件**等之间的通信

```js
import Vue from 'vue'
export const EventBus = new Vue()


methods:{
  add(){
    EventBus.$emit('addition', {
      num:this.num++
    })
  }
}


mounted() {
  EventBus.$on('addition', param => {
    this.count = this.count + param.num;
  })
}
```

### 10、vue路由懒加载

方案一：使用箭头函数+import动态加载

```js
const List = () => import('@/components/list.vue')
const router = new VueRouter({
  routes: [
    { path: '/list', component: List }
  ]
})
```

方案二：使用箭头函数+require动态加载

```js
const router = new Router({
  routes: [
   {
     path: '/list',
     component: resolve => require(['@/components/list'], resolve)
   }
  ]
})
```

方案三：使用webpack的require.ensure技术，也可以实现按需加载。 这种情况下，多个路由指定相同的chunkName，会合并打包成一个js文件。

```js
// r就是resolve
const List = r => require.ensure([], () => r(require('@/components/list')), 'list');
// 路由也是正常的写法  这种是官方推荐的写的 按模块划分懒加载 
const router = new Router({
  routes: [
  {
    path: '/list',
    component: List,
    name: 'list'
  }
 ]
}))
```

### 11、route 和router 的区别

- $route 是“路由信息对象”，包括 path，params，hash，query，fullPath，matched，name 等路由信息参数
- $router 是“路由实例”对象包括了路由的跳转方法，钩子函数等。

### 12、router路由钩子

vue-router全局有三个路由钩子：

- router.beforeEach 全局前置守卫 进入路由之前
- router.beforeResolve 全局解析守卫（2.5.0+）在 beforeRouteEnter 调用之后调用
- router.afterEach 全局后置钩子 进入路由之后

另外如果不想配置全局路由钩子，给单独某个路由配置的话可以使用`beforeEnter`，有三个参数∶ to、from、next

```js
export default [    
    {        
        path: '/',        
        name: 'login',        
        component: login,        
        beforeEnter: (to, from, next) => {          
            console.log('即将进入登录页面')          
            next()        
        }    
    }
]
```

组件内也有相应的钩子函数：

- beforeRouteEnter∶ 进入组件前触发
- beforeRouteUpdate∶ 当前地址改变并且该组件被复用时触发，举例来说，带有动态参数的路径foo/∶id，在 /foo/1 和 /foo/2 之间跳转的时候，由于会渲染同样的foa组件，这个钩子在这种情况下就会被调用
- beforeRouteLeave∶ 离开组件被调用

另外beforeRouteEnter组件内还访问不到this，因为该守卫执行前组件实例还没有被创建，需要传一个回调给 next来访问

```js
beforeRouteEnter(to, from, next) {      
    next(target => {        
        if (from.path == '/classProcess') {          
            target.isFromProcess = true        
        }      
    })    
}
```

#### 12.1 Vue路由钩子在生命周期函数的体现

完整的路由导航解析流程（不包括其他生命周期）

1. 触发进入其他路由。

2. 调用要离开路由的组件守卫beforeRouteLeave

3. 调用局前置守卫∶ beforeEach

4. 在重用的组件里调用 beforeRouteUpdate

5. 调用路由独享守卫 beforeEnter。

6. 解析异步路由组件。

7. 在将要进入的路由组件中调用 beforeRouteEnter

8. 调用全局解析守卫 beforeResolve

9. 导航被确认。

10. 调用全局后置钩子的 afterEach 钩子。

11. 触发DOM更新（mounted）。

12. 执行beforeRouteEnter 守卫中传给 next 的回调函数

#### 12.2 Vue-router 导航守卫有哪些

- 全局前置/钩子：beforeEach、beforeResolve、afterEach
- 路由独享的守卫：beforeEnter
- 组件内的守卫：beforeRouteEnter、beforeRouteUpdate、beforeRouteLeave

### 13、Redux 和 Vuex 有什么区别

- Vuex改进了Redux中的Action和Reducer函数，以mutations变化函数取代Reducer，无需switch，只需在对应的mutation函数里改变state值即可
- Vuex由于Vue自动重新渲染的特性，无需订阅重新渲染函数，只要生成新的State即可
- Vuex数据流的顺序是∶View调用store.commit提交对应的请求到Store中对应的mutation函数->store改变（vue检测到数据变化自动渲染）

### 14、Vuex有哪几种属性？

- state => 基本数据(数据源存放地)
- getters => 从基本数据派生出来的数据
- mutations => 提交更改数据的方法，同步
- actions => 像一个装饰器，包裹mutations，使之可以异步。
- modules => 模块化Vuex

### 15、Vue3.0有什么更新

1. 监测机制的改变：3.0 将带来基于代理 Proxy的 observer 实现消除了 Vue 2 当中基于 Object.defineProperty 的实现所存在的很多限制，如对数组的索引和长度修改不能检测到、添加或删除对象的属性时，Vue 检测不到。
2. 支持 Map、Set、WeakMap 和 WeakSet，检测属性的添加和删除
3. 作用域插槽，2.x 的机制导致作用域插槽变了，父组件会重新渲染，而 3.0 把作用域插槽改成了函数的方式，这样只会影响子组件的重新渲染，提升了渲染的性能。
4. 对象式的声明方式：vue2.x 中的组件是通过声明的方式传入一系列 option，和 TypeScript 的结合需要通过一些装饰器的方式来做，虽然能实现功能，但是比较麻烦。3.0 修改了组件的声明方式，改成了类式的写法，这样使得和 TypeScript 的结合变得很容易
5. 基于 tree shaking 优化，提供了更多的内置功能。

### 16、虚拟DOM

虚拟DOM是一个JavaScript对象，通过对象的方式来表示DOM结构。

虚拟DOM的解析过程：

1. 首先对将要插入到文档中的 DOM 树结构进行分析，使用 js 对象将其表示出来，比如一个元素对象，包含 TagName、props 和 Children 这些属性。然后将这个 js 对象树给保存下来，最后再将 DOM 片段插入到文档中。
2. 当页面的状态发生改变，需要对页面的 DOM 的结构进行调整的时候，首先根据变更的状态，重新构建起一棵对象树，然后将这棵新的对象树和旧的对象树进行比较，记录下两棵树的的差异。
3. 最后将记录的有差异的地方应用到真正的 DOM 树中去，这样视图就更新了。

#### 16.1 DIFF算法的原理

1. 首先，对比节点本身，判断是否为同一节点，如果不为相同节点，则删除该节点重新创建节点进行替换
2. 如果为相同节点，进行patchVnode，判断如何对该节点的子节点进行处理，先判断一方有子节点一方没有子节点的情况(如果新的children没有子节点，将旧的子节点移除)
3. 比较如果都有子节点，则进行updateChildren，判断如何对这些新老节点的子节点进行操作（diff核心）。
4. 匹配时，找到相同的子节点，递归比较子节点

