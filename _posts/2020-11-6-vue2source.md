---
layout: post
title: vue2源码仿写
categories: vue
description: vue2.x源码
keywords: vue, javascript
---

仿写一个vue2.x的源码，Object.defineProperty()

### 1、环境搭建

打包工具选择的rollup，因为rollup打包出来的代码更少，包更小，rollup更专注于JavaScript类库的打包。

环境当然还是`npm`，初始化一个npm的配置文件，`npm init -y`，安装所需依赖

```js
"devDependencies": {
    "@babel/core": "^7.12.3",
    "@babel/preset-env": "^7.12.1",
    "cross-env": "^7.0.2",
    "rollup": "^2.33.1",
    "rollup-plugin-babel": "^4.4.0",
    "rollup-plugin-serve": "^1.1.0"
  }
```

配置rollup，创建一个`rollup.config.js`的文件

配置项
```js
import babel from 'rollup-plugin-babel';
import serve from 'rollup-plugin-serve';

export default {
  input: './src/index.js', // 入口
  output: {
    file: 'dist/umd/vue.js', // 出口
    name: 'Vue', // 全局变量名字
    format: 'umd', // 模块规范
    sourcemap: true, // 开启源码调试
  },
  plugins: [
    babel({
      exclude: "node_modules/**", // 不包含node modules下的js文件
    }), 
    // 开发模式下启动这个插件
    process.env.ENV === 'development' ? serve({
      open: true,  // 默认打开
      openPage: '/public/index.html',  // 打开一个默认页面
      port: 3000, // 端口号
      contentBase: '' // 以当前目录为根目录启动
    }) : null
  ]
}
```

配置一下脚本

```js
  "scripts": {
    "build:dev": "rollup -c",
    "serve": "cross-env ENV=development rollup -c -w"
  },
```

配置一下`babel`，创建`.babelrc`
```js
{
  "presets": ["@babel/preset-env"]
}
```


总体的目录
```
└─ vuecode
   ├─ node_modules
   ├─ public
   │  └─ index.html
   ├─ src
   │  └─ index.js
   ├─ .babelrc
   ├─ .gitignore
   ├─ package-lock.json
   ├─ package.json
   ├─ README.md
   └─ rollup.config.js
```

`public/index.html`是默认的html文件，`src/index.js`是默认的入口文件，其他就不赘述了。


### 2、框架基础搭建

声明一个`Vue`的函数，接收一个参数，是用户传进来的参数, 初始化一个方法，处理参数，进行一系列操作

`src/index.js`
```js
function Vue(options) {
  this._init(options);
}

Vue.prototype._init = function(options) {
  console.log(options);
}
```

由于原型上会有很多很多方法，我们把初始化函数抽离到一个文件里面, `src/init.js`

```js
import { initState } from './state';

export function initMixin(Vue) {
  Vue.prototype._init = function(options) {
  }
}
```

`index.js`里面进行相应的修改
```js
import { initMixin } from './init.js';

function Vue(options) {
  this._init(options);
}

initMixin(Vue);

export default Vue;
```

#### 2.1 初始化状态

对vue的`初始化 属性 方法 数据 计算属性 watch`进行初始化，对数据开始监听

在初始化文件里把用户参数挂载到原型上，创建一个初始化以上各项的方法，然后抽离出来。

`src/init.js`
```js
import { initState } from './state';

export function initMixin(Vue) {
  Vue.prototype._init = function(options) {

    // 数据劫持
    const vm = this;
    // vue中使用$options来指代用户传递的属性
    vm.$options = options;

    // 初始化状态
    initState(vm);
  }
}
```

`src/state.js`进行各项的初始化，把用户传入的数据暴露出去，用户可以修改，对数据进行监听观察，创建`observe`，把`observe`抽离成一个单独的文件，这里放vue的核心，也就是响应式原理

```js
import { observe } from './observer/index.js';

export function initState(vm) {
  const opts = vm.$options;

  // 初始化 属性 方法 数据 计算属性 watch
  if (opts.props) {
    initProps(vm);
  }
  if (opts.methods) {
    initMethods(vm);
  }
  if (opts.data) {
    initData(vm);
  }
  if (opts.computed) {
    initComputed(vm);
  }
  if (opts.watch) {
    initWatch(vm);
  }
}

function initProps(vm) {};

function initMethods(vm) {};

function initData(vm) {
  // 数据的初始化
  let data = vm.$options.data;
  // vm._data: 暴露出去给用户使用
  data = vm._data = typeof data === 'function' ? data.call(vm) : data;

  // 对象劫持  用户修改了数据可以得到通知，进行一系列操作，如更新视图
  // MVVM模式  数据变化可以驱动视图变化
  // 响应式
  observe(data);
};

function initComputed(vm) {};

function initWatch(vm) {};
```

#### 2.2 对象数据劫持

也就是响应式原理，抽离到`observer/index.js`，vue2的响应式原理使用的是`object.defineProperty()`方法，该方法有兼容问题，所以IE8及以下vue2不支持。


创建一个`observe`方法，并且暴露出去, 因为这里合法值应该是`object`，需要判断用户传入的是不是一个对象，把判断方法抽离到一个单独的文件里面
`src/observer/index.js`
```js
import { isObject } from '../unit/index';
export function observe(data) {

  let isObj = isObject(data);

  if (!isObj) {
    return;
  }

  // 值合法，进行劫持
  return new Observer(data);
}
```

`src/unit/index`
```js
export function isObject(data) {
  return typeof data === 'object' && data !== null;
}
```


当数据合法，然后进行劫持，创建一个`class Observer`, 接受用户传入的值，把值的每一项使用`Object.defineProperty`进行重写, 对于复杂的值进行递龟然后再代理

`src/observer/index.js`
```js
class Observer {
  constructor(value) {
    // 监听数据，给数据添加get set方法
    // vue2.x的缺陷：如果数据层数过多，vue需要递龟去解析对象中的属性，一次增加set和get方法
    // vue3开始使用proxy，不需要递龟
    this.walk(value);
  }

  walk(data) {
    let keys = Object.keys(data); // 拿到所有的key
    // 拿到每个key和value，然后对数据里进行劫持
    keys.forEach(key => defineReactive(data, key, data[key]))
  }
}

function defineReactive(data, key, value) {
  observe(value); // 递龟进行深度检测
  Object.defineProperty(data, key, {
    get() {
      return value;
    },
    set(newValue) {
      if (newValue === value) return;
      observe(newValue); // 如果用户输入的值仍然是对象，继续劫持 
      value = newValue;
    }
  })
}
```

简单描述一下上面的逻辑：
1. 用户数据传入，拿到对象的所有的key，然后遍历数据，把每一项数据进行代理
2. 重写`get()/set()`方法
3. 因为用户的值可能是{a:{v: 1}}这种类型的值，所以进行递归遍历代理，把所有的值都进行代理
4. 当用户修改一个值的时候可能会这样修改`vm._data.a = {a:2}`，这个时候用户传入的值是对象，需要对用户传入的值再进行一次劫持。


#### 2.3 数组的劫持

vue2对数组的劫持是重写数组的方法，然后触发劫持，vue3使用proxy就不需要，直接代理。

把数组和对象劫持分开做判断，数组里的每一项还可能是对象，所以需要遍历数组的每一项，然后去劫持
```js
class Observer {
  constructor(value) {
    // ...其他

    if (Array.isArray(value)) {
      this.observerArray(value); // 数组监听  不会对索引进行监听，会有性能问题，数组里是对象的话才监听
    } else {
      this.walk(value);
    }
  }

  observerArray(value) {
    // 把用户传入的数组里的每一项进行劫持
    for (var i = 0; i < value.length; i++) {
      observe(value[i]);
    }
  }
  // ...其他
}
```

上面就可以简单监听到数据的值了，但是数组的改变时候调用方法还未监听，vue是重写的原方法

数组的方法逻辑比较多，抽离到单独的文件里面`array.js`，然后创建一个新对象`arrayMethods`，原型指向数组原生的方法，然后导出
我们将在创建的新对象上重写数组的方法

```js
// array.js
// 只劫持会修改原数组的方法： push  shift unshift  pop reverse sort splice

let oldArrayMethods = Array.prototype;

export const arrayMethods = Object.create(oldArrayMethods);
```

把导出的方法放到数据的原型上
```js
// observer/index.js

if (Array.isArray(value)) {
  value.__proto__ = arrayMethods; // 数组操作被监听是因为它的方法是被重写的

  this.observerArray(value); // 数组监听  不会对索引进行监听，会有性能问题，数组里是对象的话才监听
} else {
  this.walk(value);
}

```

然后我们来重写数组的方法
```js
// array.js

const methods = ['push', 'pop', 'shift', 'unshift', 'reverse', 'sort', 'splice'];

methods.forEach(method => {
  arrayMethods[method] = function(...args) {
    let result = oldArrayMethods[method].apply(this, args);
    return result;
  }
})
```

这样就重写好了，但是还有问题，如果数组的操作是新增的话，新增的数据是没有被劫持的，所以我们需要修改一下，根据新增的那几个方法来判断劫持

```js
methods.forEach(method => {
  arrayMethods[method] = function(...args) {
    let result = oldArrayMethods[method].apply(this, args);
    let inserted;  // 新增的值
    // 当原数组有新增的时候,需要重新判断监听新增的值，进行劫持
    switch (method) {
      case 'push':
      case 'unshift':
        inserted = args;
        break;
      case 'splice':
        inserted = args.slice(2);
        break;
      default:
        break;
    }

    return result;
  }
})
```

数组新增的值需要重新遍历进行劫持，但是劫持的方法在`observer/index.js`里面，我们需要在这里访问到，所以挂载到原型上。

```js
// observer/index.js
// 这里需要设置一下不可枚举，不然value挂载的this示例在后面的遍历中会被接着遍历，导致爆栈

Object.defineProperty(value, '__ob__', {
  enumerable: false, //不可枚举
  configurable: false, // 不可配置
  value: this
})

if (Array.isArray(value)) {
  // ...
}

```

这样就可以在`array.js`里面访问到了,就可以进行劫持。


```js
methods.forEach(method => {
  arrayMethods[method] = function(...args) {
    let result = oldArrayMethods[method].apply(this, args);
    let inserted;
    let ob = this.__ob__;
    // 当原数组有新增的时候,需要重新判断监听新增的值是否需要劫持
    switch (method) {
      case 'push':
      case 'unshift':
        inserted = args;
        break;
      case 'splice':
        inserted = args.slice(2);
        break;
      default:
        break;
    }

    // 如果数组有新增，则对新增的值进行劫持
    if (inserted) ob.observerArray(inserted);
    return result;
  }
})
```

截止，数组的劫持就好了，我们可以把挂载`__ob__`设置不可枚举的方法抽离到`unit/index.js`里面
```js
export function def(data, key, value) {
  Object.defineProperty(data, key, {
    enumerable: false, //不可枚举
    configurable: false, // 不可配置
    value: value
  })
}
```

然后使用的地方引用一下，使用一下
```js
import { isObject, def } from '../unit/index';

class Observer {
  constructor(value) {
    def(value, '__ob__', this);
    // ...
  }
}
```



