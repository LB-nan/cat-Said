---
layout: post
title: vue2源码解析
categories: vue
description: vue2.x源码
keywords: vue, javascript
---

仿写一个简易版vue2.x的源码核心部分，加深对vue的理解，不过多考虑各种边界与问题。

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
import { proxy } from './unit/index';

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


  // 取值时代理，可以vm.name这样直接拿到vm._data.name
  for (let key in data) {
    proxy(vm, '_data', key);
  }

  // 对象劫持  用户修改了数据可以得到通知，进行一系列操作，如更新视图
  // MVVM模式  数据变化可以驱动视图变化
  // 响应式
  observe(data);
};

function initComputed(vm) {};

function initWatch(vm) {};
```

```js
// unit/index.js
export function proxy(vm, source, key) {
  Object.defineProperty(vm, key, {
    get() {
      return vm[source][key];
    },
    set(newVal) {
      vm[source][key] = newVal;
    }
  })
}

```

### 3、 对象数据劫持

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


### 4、数组的劫持

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

### 5、模板解析

数据都劫持完开始处理页面，当用户传入了`el`属性的时候，就要渲染挂载元素，渲染模板。这个地方需要考虑vue编译的优先级`ender() => template() => el属性`,在`init.js`里面,这里先弄模板编译

```js
// init.js

import { compileToFunction } from './compiler/index.js';

export function initMixin(Vue) {
  // ...

  Vue.prototype.$mount = function(el) {
    const vm = this;
    const options = vm.$options;
    // 拿到传进来的元素节点
    el = document.querySelector(el);

    // 如果有render方法就走render方法
    if (!options.render) {
      // 如果不存在render方法就走模板编译
      let template = options.template; // 获取模板
      // 如果不存在template就把el的节点设为template
      if (!template && el) {
        template = el.outerHTML; // 获取到节点及节点的所有子节点
      }
      // 取到模板然后进行编译,编译完成然后渲染   compileToFunction()抽离到另外文件
      const render = compileToFunction(template);
      options.render = render;
    } else {
      // render ...
    }
  }
}

```


把模板传递过去，我们需要把他转化成一个render方法，模板需要先解析成一个树，描述节点关系
假设一段HTML代码`<div id="app"><p>aaa</p></div>`是传入的模板，我们需要先匹配到开始标签`<`然后匹配出标签的名字`div`,这个时候就获取到当前的标签是什么标签了，然后再匹配属性`id="app"`，当然也可能没属性，然后接下来匹配结束标签`>`，然后匹配文字，然后又是开始标签循环，每一次匹配都把匹配到的截取，这样慢慢就匹配完一整个字符串语句，

这里没有考虑一些很特殊的情况，比如不闭合标签之类的。

```js
// compiler/index.js
const ncname = `[a-zA-Z_][\\-\\.0-9_a-zA-Z]*`; // 匹配字符串类型：abc-aaa
const qnameCapture = `((?:${ncname}\\:)?${ncname})`; // 匹配字符串类型：<aaa:asdads>
const startTagOpen = new RegExp(`^<${qnameCapture}`); // 标签开头的正则 捕获的内容是标签名
const endTag = new RegExp(`^<\\/${qnameCapture}[^>]*>`); // 匹配标签结尾的 </div>
const attribute = /^\s*([^\s"'<>\/=]+)(?:\s*(=)\s*(?:"([^"]*)"+|'([^']*)'+|([^\s"'=<>`]+)))?/; // 匹配属性的
const startTagClose = /^\s*(\/?)>/; // 匹配标签结束的 >  <div>
const defaultTagRE = /\{\{((?:.|\r?\n)+?)\}\}/g

function start(tagName, attrs) {
  console.log(tagName, attrs);
}

function chars(text) {
  console.log('文字', text)
}

function end(tagName) {
  console.log('结束：', tagName)
}

function parseHTML(html) {
  // 不停的去解析html字符串
  while (html) {
    let textEnd = html.indexOf('<');
    if (textEnd === 0) {
      // 如果当前索引为0，肯定是一个标签  解析标签
      let startTagMatch = parseStartTag();
      if (startTagMatch) { // 如果开始标签匹配完毕，然后还需要匹配结束标签
        start(startTagMatch.tagName, startTagMatch.attrs);
        continue;
      }

      let endTagMatch = html.match(endTag);
      if (endTagMatch) { // 如果匹配到结束标签，则继续截取
        advance(endTagMatch[0].length);
        end(endTagMatch[1]);
        continue;
      }
    }
    let text;
    if (textEnd >= 0) {
      text = html.substring(0, textEnd);
      console.log(text);
    }
    if (text) {
      advance(text.length);
      chars(text);
    }
  }

  // 用作截取字符串 
  function advance(n) {
    html = html.substring(n);
  }

  function parseStartTag() {
    // 匹配当前标签，匹配到之后把原数据`html`截取，保留后面的  例如： <div id="app"><p>aaa</p></div>
    let start = html.match(startTagOpen)
    if (start) {
      const match = {
          tagName: start[1],
          attrs: []
        }
        // 用匹配到的标签的长度去截取  | 变成 id="app"><p>aaa</p></div>
      advance(start[0].length);

      // 匹配属性，当有属性的时候就进行截取，直到没有 | 变成 ><p>aaa</p></div>
      let end, attr;
      while (!(end = html.match(startTagClose)) && (attr = html.match(attribute))) {
        advance(attr[0].length);
        // 把取到的属性存到match里面
        match.attrs.push({ name: attr[1], value: attr[3] || attr[4] || attr[5] });
      }

      // 去掉 >   还是使用截取
      if (end) {
        advance(end[0].length);
        return match;
      }
    }
  }
  return root;
}


export function compileToFunction(template) {
  let root = parseHTML(template);
  return function render() {

  }
}
```

通过上面可以匹配出所需的各个节点，我们就可以组装出一颗ast语法树了。

```js

let root = null; // 根节点
let currentParent = null;
// 栈，存储标签名称，做对比，标签是否合法，从头push，遇到一个push一个
// 当遇到挨着的重复的就出栈一个作为一个正常闭合的标签。当栈空就说明数据合法，完成还有剩余说明有标签不合法，未闭合或输错其他
let stack = [];
const ELEMENT_TYPE = 1;
const TEXT_TYPE = 3;

function createASTElement(tagName, attrs) {
  return {
    tag: tagName,
    type: ELEMENT_TYPE,
    children: [],
    attrs: attrs,
    parent: null
  }
}

function start(tagName, attrs) {
  // 遇到开始标签开始创建ast树
  let element = createASTElement(tagName, attrs);
  if (!root) {
    root = element;
  }
  currentParent = element;
  stack.push(element);
}

function chars(text) {
  text = text.replace(/\s/g, '');
  if (text) {
    currentParent.children.push({
      text,
      type: TEXT_TYPE
    })
  }
}

function end(tagName) {
  // 把当前的最后一个出栈，如果传入的tag和element相同，则合法，是一个闭合的标签
  let element = stack.pop();
  if (tagName == element.tag) {
    // 当前元素的父级肯定是他前面的那个元素
    currentParent = stack[stack.length - 1];
    // 如果有父级
    if (currentParent) {
      element.parent = currentParent;
      currentParent.children.push(element);
    }
  }
}
```

简单描述一下上面代码逻辑
1. 创建一个根节点作为ast的根，然后创建一个当前元素的parent，创建一个栈，还有节点类型
2. 当代码解析到开始标签的时候也就是`start()`，我们创建一个对象，判断有没有根，没有的话当前对象就是根，就是父节点，然后赋值当前的父节点为当前的节点，然后把元素放到栈里面
3. 如果遇到文字节点则创建文字节点，当前文字节点的父级就是全局的`currentParent`，直接push到父节点里面就好
4. 如果匹配到结束标签，则把最后一个元素拿出来对比，如果和传入的元素名字一致，则两个是同一个元素，可以正常闭合，出栈，然后当前栈里的最后一个元素就是当前元素的父节点，修改父子的对应关系后，继续匹配。
5. 当传入的html字符串也就是模板全部匹配完成之后，代码结束，返回`root`。

这个时候一个描述dom对象的ast语法树的对象就拿到了，然后就要进行`render()`了。


### 6、模板引擎

在拿到`root`的地方，处理ast语法树，创建一个函数来处理,需要处理成一个字符串`_c(tagName,{attr:value,...},children,children...)`这种格式

```js
// compiler/index.js
export function compileToFunction(template) {
  let root = parseHTML(template);
  console.log(root);

  // ast语法树转成js
  let code = generate(root);

  return function render() {

  }
}
```

`generate()`方法接受一个参数，就是`root`，根据前面的格式处理字符串，属性和子元素的单独抽离方法进行处理。
```js
function generate(el) {
  let code = `_c ("${el.tag}",${el.attrs.length ? genProps(el.attrs): 'undefined'}${el.children ? `,${genChildren(el)}` : ''})`;
  return code;
}
```


处理属性的方法，没有考虑复杂的属性的情况, 属性是个数组，传递过来的值格式是`[{name: 'id', value:: 'app'}]`
```js
// 处理属性
function genProps(attrs) {
  let str = '';
  for (let i = 0; i < attrs.length; i++) {
    let attr = attrs[i];
    if (attr.name === 'style') { // 如果当前属性是css
      let obj = {};
      attr.value.split(';').forEach(item => {
        let [key, value] = item.split(':');
        obj[key] = value;
      })
      attr.value = obj;
    }
    // attr.value有可能是对象，所以JSON.stringify();
    str += `${attr.name}:${JSON.stringify(attr.value)},`;
  }
  return `{${str.slice(0,-1)}}`;
}
```

处理子元素的方法，子元素里面还可能是节点，所以需要递归处理
```js
// 处理子节点
function genChildren(el) {
  let children = el.children;
  if (children && children.length > 0) {
    return `${children.map(child => gen(child)).join(',')}`
  } else {
    return false;
  }
}

function gen(node) {
  if (node.type == 1) {
    // 元素标签
    return generate(node);
  } else {
    // 文本
    let text = node.text;
    return `_v(${text})`;
  }
}

```

需要判断传入的元素的子元素是否存在，`children`是一个数组，所以需要遍历，每一项需要判断是元素还是文本节点，元素节点的话回调处理元素节点，继续之前的拼接，文字节点的话存起来，这里还有特殊情况未处理，如：`{{name}}`

那来处理`{{name}}`这种的代码
```js

function gen(node) {
  if (node.type == 1) {
    // 元素标签
    return generate(node);
  } else {
    // 文本
    let text = node.text; // xxx {{name}} xxx {{age}} => 'xxx' + _s(name) + 'xxx' + _s(age); 
    // 存放匹配的结果
    let tokens = [];
    // 匹配到的值，索引
    let match, index;
    // 索引
    let lastIndex = defaultTagRE.lastIndex = 0;
    while (match = defaultTagRE.exec(text)) {
      // 当前匹配到的位置
      index = match.index;
      // 如果当前截取到的位置大于正则索引位置则把匹配到的那一段数据放到tokens里面
      if (index > lastIndex) {
        tokens.push(JSON.stringify(text.slice(lastIndex, index)));
      }
      // 匹配到{{age}}的时候把ags放到`_s()`的方法里面
      tokens.push(`_s(${match[1].trim()})`);
      // 然后把当前的正则匹配索引置 为  当前匹配的位置+截取的数据的长度，也就是下一次开始的位置
      lastIndex = index + match[0].length;
    }
    // 如果最后还有剩余，则push到最后面。
    if (lastIndex < text.length) {
      tokens.push(JSON.stringify(text.slice(lastIndex)));
    }
    // 用`+`号连接
    return `_v(${tokens.join('+')})`;

    // 到此为止，拼接完毕

    /*
    HTML模板： 
      <div id="app">
        <p>vvv{{name}}</p>
      </div>

    转化后的字符串： _c("div",{id:"app},_c("p",undefined,_v("vvv"+_s(name))))
    */
  }
}
```

就把一个模板解析成一串字符串了，可以用于render函数使用了，render函数返回的是一个虚拟DOM(VDOM)，而且还需要绑定一下`this`到实例上，比如数据`_c("div",{id:"app},_c("p",undefined,_v("vvv"+_s(name))))`这里的`name`是需要去`vm`也就是实例上获取的。这个时候就需要`with`了。

```js
export function compileToFunction(template) {
  let root = parseHTML(template);

  // ast语法树转成js
  let code = generate(root);
  // 使用with包装起来
  let renderFn = new Function(`with(this){ return ${code}}`);

  return renderFn;
}
```

这样就封装好了，我们在`init.js`里面打印一下，可以拿到如下结果
```js
ƒ anonymous() {
  with(this){ return _c("div",{id:"app"},_c("p",undefined,_v("vvv"+_s(name))))}
}
```

现在开始调用render()进行初始化渲染流程，准备生成虚拟DOM了。

> with: JavaScript查找某个未使用命名空间的变量时，会通过作用域链来查找，作用域链是跟执行代码的context或者包含这个变量的函数有关。'with'语句将某个对象添加到作用域链的顶部，如果在statement中有某个未使用命名空间的变量，跟作用域链中的某个属性同名，则这个变量将指向这个属性值。如果沒有同名的属性，则将拋出ReferenceError异常。——MDN
 

### 7、初始化渲染

渲染就需要挂载组件，创建一个方法，挂载组件，

```js
import { mountComponent } from './lifecycle.js'

Vue.prototype.$mount = function(el) {
  const vm = this;
  const options = vm.$options;
  el = document.querySelector(el);

  // ...

  // 渲染当前组件，挂载组件
  mountComponent(vm, el);
}
```

挂载方法里面需要通过render创建虚拟dom，然后通过update创建真实dom

```js
// lifecycle.js

export function mountComponent(vm, el) {
  const options = vm.$options;
  vm.$el = el;

  // watcher 用来渲染
  // vm._render 通过解析的render方法，渲染出虚拟dom
  // vm._update 通过虚拟dom，创建真实dom

  // 渲染页面
  let updateComponent = () => {
    vm._update(vm._render());
  }

  // 渲染watcher，每个组件都有一个watcher，   true表示是一个渲染watcher
  new Watcher(vm, updateComponent, () => {}, true);
}
```

创建`_render()`和`_update`方法，把他们加到原型上面。

```js
// index.js
import { initMixin } from './init.js';
import { renderMixin } from './render.js';
import { lifecycleMixin } from './lifecycle.js';

function Vue(options) {
  this._init(options);
}

initMixin(Vue);
renderMixin(Vue);
lifecycleMixin(Vue);

export default Vue;



// render.js
export function renderMixin(Vue) {
  Vue.prototype._render = function (){
    
  }
}


// lifecycle.js
export function lifecycleMixin(Vue){
  Vue.prototype._update = function(){
    
  }
}

```

都创建完成之后我们来写watcher

```js
class Watcher {
  constructor(vm, exprOrFn, callback, options) {
    this.vm = vm;
    this.callback = callback;
    this.options = options;

    this.getter = exprOrFn; // 将内部传过来的回调函数 放到getter属性上
    this.get();
  }
  get() {
    this.getter();
  }
}

export default Watcher
```

#### 7.1 生成虚拟dom

在watcher里面调用了`_render()`，来完善`_render()`;
```js
export function renderMixin(Vue) {

  Vue.prototype._render = function() {
    const vm = this;
    const { render } = vm.$options;
    console.log(render);
  }
}
```
打印结果
```js
ƒ anonymous(
) {
with(this){ return _c("div",{id:"app"},_c("p",undefined,_v("vvv"+_s(name))))}
}
```

我们执行`render`，来生成vdom。

```js
render.call(vm);
```
报错` _c is not defined`，`_c()`创建节点,`_v()创建文字节点`,`_s()对对象进行一次JSON.stringify()`，来完善这些方法。

```js
import { createElement, createTextNode } from './vdom/create-element.js';
export function renderMixin(Vue) {

  // 创建节点
  Vue.prototype._c = function() {
      return createElement(...arguments);
    }
    // 创建文字节点
  Vue.prototype._v = function(text) {
      return createTextNode(text);
    }
    // json.stringify()
  Vue.prototype._s = function(val) {
    return val == null ? '' : (typeof val === 'object' ? JSON.stringify(val) : val);
  }

  Vue.prototype._render = function() {
    const vm = this;
    const { render } = vm.$options;
    let vnode = render.call(vm);
    return vnode;
  }
}
```

把创建节点和文本节点的方法抽离出去了，下面是代码
```js
//  ./vdom/create-element.js
export function createElement(tag, data = {}, ...children) {
  let key = data.key;
  if (key) {
    delete data.key;
  }
  return vnode(tag, data, key, children, undefined);
}
export function createTextNode(text) {
  return vnode(undefined, undefined, undefined, undefined, text);
}

function vnode(tag, data, key, children, text) {
  return {
    tag,
    data,
    key,
    children,
    text
  }
}
```

`render`返回了一个虚拟dom，就可以在`lifecycle`里面的`_update()`里拿到了，

```js
// lifecycle.js
export function lifecycleMixin(Vue) {
  Vue.prototype._update = function(vnode) {
    // 通过虚拟节点渲染出真实dom
    console.log(vnode);
  }
}

```




