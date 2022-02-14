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

#### 1.1 vue模板编译原理

vue中的模板template无法被浏览器解析并渲染，因为这不属于浏览器的标准，不是正确的HTML语法，所有需要将template转化成一个JavaScript函数，这样浏览器就可以执行这一个函数并渲染出对应的HTML元素，就可以让视图跑起来了，这一个转化的过程，就成为模板编译。模板编译又分三个阶段，解析parse，优化optimize，生成generate，最终生成可执行函数render。

- **解析阶段**：使用大量的正则表达式对template字符串进行解析，将标签、指令、属性等转化为抽象语法树AST。
- **优化阶段**：遍历AST，找到其中的一些静态节点并进行标记，方便在页面重渲染的时候进行diff比较时，直接跳过这一些静态节点，优化runtime的性能。
- **生成阶段**：将最终的AST转化为render函数字符串。render返回的虚拟DOM。

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
6. updated(更新后)：在由于数据更改导致的虚拟DOM重新渲染和打补丁之后调用。此时 DOM 已经根据响应式数据的变化更新了。调用时，组件 DOM已经更新，所以可以执行依赖于DOM的操作。然而在大多数情况下，应该避免在此期间更改状态，因为这可能会导致更新无限循环。该钩子在服务器端渲染期间不被调用。
8. beforeDestory(销毁前)：实例销毁之前调用。这一步，实例仍然完全可用，`this` 仍能获取到实例。
9. destroyed(销毁后)：实例销毁后调用，调用后，Vue 实例指示的所有东西都会解绑定，所有的事件监听器会被移除，所有的子实例也会被销毁。该钩子在服务端渲染期间不被调用。

一般created/beforeMount/mounted 皆可 正常获取在 created 里面即可，而且服务端渲染只支持created；如果涉及到需要页面加载完成之后(DOM操作)的就用 mounted。

另外还有 `keep-alive` 独有的生命周期，分别为 `activated` 和 `deactivated` 。用 `keep-alive` 包裹的组件在切换时不会进行销毁，而是缓存到内存中并执行 `deactivated` 钩子函数，命中缓存渲染后会执行 `activated` 钩子函数。

### 4、常见的事件修饰符

1. .stop：等同于 JavaScript 中的 `event.stopPropagation()` ，防止事件冒泡
2. .prevent：等同于 JavaScript 中的 `event.preventDefault()` ，防止执行预设的行为（如果事件可取消，则取消该事件，而不停止事件的进一步传播）
3. .capture：与事件冒泡的方向相反，事件捕获由外到内
4. .self：只会触发自己范围内的事件，不包含子元素
5. .once：只会触发一次
5. .sync：同步，它会被扩展为一个自动更新父组件属性的 v-on 监听器

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

### 8、computed和watch的区别

对于computed：

- 支持缓存，只有依赖的数据发生了变化，才会重新计算
- 不支持异步，当computed中有异步操作时，无法监听数据的变化
- computed默认走缓存，计算属性是基于他们的响应式依赖进行缓存的，也就是基于data声明过，或者父组件传递的props中的数据进行计算的
- 如果一个属性是由其他属性计算而来的，这个属性依赖其他的属性，那么一般都会使用computed
- 如果computed属性的属性值是函数，那么默认使用get方法，函数的返回值就是属性的属性，属性变化时，会调用set方法

对于watch：

- 它不支持缓存，数据变化就会触发相应的操作
- 支持异步监听
- 监听的函数接受两个参数，第一个参数是最新的值，第二个是变化之前的值
- 当一个属性发送变化时，就需要执行相应的操作
- 监听的数据必须是data中声明的或者父组件传递过来的props中的数据
- 当需要执行异步或者昂贵的操作以响应不断的变化时，就需要使用watch、

总结：

- computed：依赖其他属性值，并且computed的值有缓存，只有它依赖的属性发生改变，下一次computed获取的值才会重新计算
- watch：更多是观察的作品，无缓存，类似于某些数据的监听回调，每当监听的数据变化时都会执行回调进行后续操作

### 9、组件通信

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

#### 9.1 父子组件通信

1. props&emit：子组件通过props属性来接受父组件的数据，然后父组件在子组件上注册监听事件，子组件通过$emit触发事件来向父组件发送数据
2. ref：通过ref属性给子组件设置一个名字，父组件通过`$refs`组件名来获得子组件，子组件通过`$parent`获得父组件，这样也可以实现通信
3. provide/inject：在父组件中通过provide提供变量，在子组件中通过inject来讲变量注入到组件中，不论子组件有多深，只要调用了inject那么就可以注入provide中的数据

子组件不能直接修改父组件，需要父组件定义一个监听的自定义事件，子组件进行`$emit`来触发，父组件监听到后，由父组件修改

#### 9.2 兄弟组件通信

1. eventBus
2. 通过`$parent/$refs`获取到兄弟组件也可以进行通信

#### 9.3 任意组件通信

使用eventBus

### 10、eventBus事件总线

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

### 11、vue路由懒加载

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

### 12、route 和router 的区别

- $route 是“路由信息对象”，包括 path，params，hash，query，fullPath，matched，name 等路由信息参数
- $router 是“路由实例”对象包括了路由的跳转方法，钩子函数等。

### 13、router路由钩子

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

#### 13.1 Vue路由钩子在生命周期函数的体现

完整的路由导航解析流程（不包括其他生命周期）

1. 触发进入其他路由。

2. 调用要离开路由的组件守卫beforeRouteLeave

3. 调用全局前置守卫∶ beforeEach

4. 在重用的组件里调用 beforeRouteUpdate

5. 调用路由独享守卫 beforeEnter。

6. 解析异步路由组件。

7. 在将要进入的路由组件中调用 beforeRouteEnter

8. 调用全局解析守卫 beforeResolve

9. 导航被确认。

10. 调用全局后置钩子的 afterEach 钩子。

11. 触发DOM更新（mounted）。

12. 执行beforeRouteEnter 守卫中传给 next 的回调函数

#### 13.2 Vue-router 导航守卫有哪些

- 全局前置/钩子：beforeEach、beforeResolve、afterEach
- 路由独享的守卫：beforeEnter
- 组件内的守卫：beforeRouteEnter、beforeRouteUpdate、beforeRouteLeave

### 14、Redux 和 Vuex 有什么区别

- Vuex改进了Redux中的Action和Reducer函数，以mutations变化函数取代Reducer，无需switch，只需在对应的mutation函数里改变state值即可
- Vuex由于Vue自动重新渲染的特性，无需订阅重新渲染函数，只要生成新的State即可
- Vuex数据流的顺序是∶View调用store.commit提交对应的请求到Store中对应的mutation函数->store改变（vue检测到数据变化自动渲染）

### 15、Vuex

Vuex 是一个专为Vue.js开发的状态管理工具，每一个Vuex的核心就是store，包含着应用中大部分的状态state

- vuex的状态存储是响应式的，当vue组件从store中读取状态的时候，若状态发生变化，那么组件内也会相应的改变
- 改变store中的state的唯一途径就是显示的提交(commit)mutation

执行顺序：

1. 在vue的组件中触发`dispatch`一些事件或动作`actions`
2. 然后在actions中去commit提交一个mutation，因为vuex中，数据的修改只能通过mutations去修改
3. 在mutation中去改变state的数据
4. 当state的数据被修改之后，会重新渲染到vue的组件中，触发组件的更新

#### 15.1 vuex有哪几种属性？

- state => 基本数据(数据源存放地)
- getters => 从基本数据派生出来的数据
- mutations => 提交更改数据的方法，同步，如果需要异步触发就需要异步提交action然后由action commit一个mutation去触发更新
- actions => 像一个装饰器，包裹mutations，使之可以异步。actions只能由`dispatch`触发
- modules => 模块化Vuex

#### 15.2 action和mutation的区别

mutation中的操作是一系列的同步函数，用于修改state中变量的状态，当使用vuex时需要通过commit来提交需要操作的内容。每个mutation都有一个事件类型和一个回调函数，该回调函数会接受state作为第一个参数

```js
const store = new Vuex.Store({
  state: {
    count: 1
  },
  mutations: {
    increment (state) {
      state.count++      // 变更状态
    }
  }
})
```

触发

```js
store.commit('increment')
```

action类似于mutation，不同的是action可以包含任意异步操作，action提交的是mutation，而不是直接改变state的状态

```js
const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    increment (state) {
      state.count++
    }
  },
  actions: {
    increment (context) {
      context.commit('increment')
    }
  }
})
```

#### 15.3 为什么mutation不能异步

每个mutation执行完成后都会对应到一个新的状态变更，这样devtools就可以打个快照存下来，然后就可以实现 time-travel 了。如果mutation支持异步操作，就没有办法知道状态是何时更新的，无法很好的进行状态的追踪，给调试带来困难。

### 16、Vue3.0有什么更新

1. 监测机制的改变：3.0 将带来基于代理 Proxy的 observer 实现消除了 Vue 2 当中基于 Object.defineProperty 的实现所存在的很多限制，如对数组的索引和长度修改不能检测到、添加或删除对象的属性时，Vue 检测不到。
2. 支持 Map、Set、WeakMap 和 WeakSet，检测属性的添加和删除
3. 作用域插槽，2.x 的机制导致作用域插槽变了，父组件会重新渲染，而 3.0 把作用域插槽改成了函数的方式，这样只会影响子组件的重新渲染，提升了渲染的性能。
4. 对象式的声明方式：vue2.x 中的组件是通过声明的方式传入一系列 option，和 TypeScript 的结合需要通过一些装饰器的方式来做，虽然能实现功能，但是比较麻烦。3.0 修改了组件的声明方式，改成了类式的写法，这样使得和 TypeScript 的结合变得很容易
5. 基于 tree shaking 优化，提供了更多的内置功能。

### 17、虚拟DOM

虚拟DOM是一个JavaScript对象，通过对象的方式来表示DOM结构。

虚拟DOM的解析过程：

1. 首先对将要插入到文档中的 DOM 树结构进行分析，使用 js 对象(AST语法树)将其表示出来，比如一个元素对象，包含 TagName、props 和 Children 这些属性。然后将这个 js 对象树(AST语法树)给保存下来，最后再将 DOM 片段插入到文档中。
2. 当页面的状态发生改变，需要对页面的 DOM 的结构进行调整的时候，首先根据变更的状态，重新构建起一棵对象树，然后将这棵新的对象树和旧的对象树进行比较，记录下两棵树的的差异。DIFF算法
3. 最后将记录的有差异的地方应用到真正的 DOM 树中去，这样视图就更新了。

#### 17.1 DIFF算法的原理

1. 首先，对比节点本身，判断是否为同一节点，如果不为相同节点，则删除该节点重新创建节点进行替换
2. 如果为相同节点，进行patchVnode，判断如何对该节点的子节点进行处理，先判断一方有子节点一方没有子节点的情况(如果新的children没有子节点，将旧的子节点移除)
3. 比较如果都有子节点，则进行updateChildren，判断如何对这些新老节点的子节点进行操作（diff核心）。
4. 匹配时，找到相同的子节点，递归比较子节点

### 18、$nextTick原理及作用

原理就是JavaScript的eventloop的一种应用

核心利用了Promise来模拟微任务

nextTick 不仅是 Vue 内部的异步队列的调用方法，同时也允许开发者在实际项目中使用这个方法来满足实际应用中对 DOM 更新数据时机的后续逻辑处理

使用：

- 当数据变化后执行某个操作，而这个操作需要使用随数据变化而变化的DOM结构的时候，需要把方法写在`nextTick()`的回调中
- 在vue生命周期中，如果需要在created()钩子里进行DOM操作，也需要放在`nextTick()`的回调函数里

### 19、自定义指令

- 全局定义：`Vue.directive("focus",{})`
- 局部定义：`directives:{focus:{}}`
