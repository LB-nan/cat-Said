---
layout: post
title: 前端面试题之webpack
categories: 面试题
description: 面试题,webpack
keywords: 面试题, webpack, 打包, 工程化
---

webpack常问知识点

### 1、webpack简介

- 是一个用于现代javaScript应用程序的 `静态模块打包工具`。
- 当webpack处理应用程序时，会在内部构建一个依赖图，此依赖图对应影射到项目的每个模块，并声称一个或者多个bundle

### 2、webpack核心

- 入口：entry
- 输出：output
- 加载器：loader
- 插件：plugin
- 模式：mode

### 3、webpack打包流程

1. 从配置文件`webpack.config.js`和shell语句中读取并合并参数，拿到最终的参数
2. 用上一步的参数初始化Compiler对象，加载所有配置的插件，让插件监听 webpack 构建生命周期的事件节点，执行对象的run方法开始编译
3. 根据entry找到所有的入口文件
4. 从 entry 入口文件出发，调用loader对模块进行翻译，再找出该模块依赖的模块，进行递归本步骤，直到所有的入口依赖的文件都经过处理
5. 使用loader翻译完所有的模块之后（将解析出来的AST语法树转化为code），得到了每个模块被翻译后的最终内容和它们之间的依赖关系
6. 根据入口和模块之间的依赖关系，组成成一个个包含多个模块的Chunk，再将Chunk转换成一个单独的文件加入到输出列表，这一步是可以修改输出内容的最后机会
7. 确定好输出内容之后，根据配置确定输出的路径和文件名，将内容写入到文件系统

### 4、loader和plugin的区别

- loader是转换器，能够加载资源文件，并对这些文件进行处理，比如编译压缩等，最终打包到指定的文件中
  - loader接受三个参数：`source/sourceMap(可选)/data(可选)`
  - 处理一个文件可以使用多个loader，loader的执行顺序和配置的顺序是相反的，即最后一个loader最先执行，第一个loader最后执行
  - 前面执行的loader的返回值是后一个loader的入参，最后执行的loader会返回此模块的JavaScript源码
  
- plugin是插件，在webpack运行的生命周期中会广播许多事件，plugin可以监听这些事件，在合适的时候通过webpack的钩子改变输出结果

plugin是针对webpack打包的过程，它不直接操作文件，而是基于事件机制工作，会监听 webpack 打包过程中的某些事件钩子，执行任务。plugin 比 loader 强大，通过plugin 可以访问 compliler 和 compilation 过程，通过钩子拦截 webpack 的执行。

#### 4.1 为什么需要loader

本质上是因为 Webpack 只认识符合 JavaScript 规范的文本，对图片、json、Vue单文件组件等场景就不行了，就需要 Loader 介入将资源转化成 Webpack 可以理解的内容形态。

### 5、webpack进行模块加载

同步加载：直接通过`webpack_require`加载

异步加载：使用`require.ensure()`

### 6、常用的loader

- Css-loader：支持css文件的加载和解析，Webpack 是用 JS 写的，运行在 node 环境，所以默认 Webpack 打包的时候只会处理 JS 之间的依赖关系，如果在 JS 中导入了 css，那么就需要使用 css-loader 来识别这个模块
- Style-loader：css-loader 只会把 css 模块加载到 JS 代码中，并不会使用这个模块，style-loader 是通过一个 JS 脚本创建一个 style 标签，把css内容加载进去
- file-loader：
- 在css文件中定义 background 的属性或者在html中引入 image 的src，我们知道在 webpack 打包后这些图片会打包至定义好的一个文件夹下，和开发时候的相对路径会不一样，这就会导致导入图片路径的错误。而 file-loader 正是为了解决此类问题而产生的，他修改打包后图片的储存路径，再根据配置修改我们引用的路径，使之对应引入。
- url-loader：
- 如果页面图片较多，发很多http请求，会降低页面性能。这个问题可以通过 url-loader 解决。url-loader 会将引入的图片编码，生成 dataURl 并将其打包到文件中，最终只需要引入这个 dataURL 就能访问图片了。当然，如果图片较大，编码会消耗性能。因此 url-loader 提供了一个limit参数，小于 limit 字节的文件会被转为 DataURl，大于 limit 的还会使用 file-loader 进行copy。url-loader 内置了 file-loader。
- Babel-loader：Babel是一个 JavaScript 编译器，可以把新的js语法编译成兼容旧版本的，能够让我们放心的使用新一代JS语法。

### 7、常用的plugin

- **clean-webpack-plugin**：每次打包后自动清理dist目录。
- **html-webpack-plugin**：相比于之前写死HTML文件的方式，自动生成HTML的优势在于： 
  - 1.HTML也输出到dist目录中了，上线时只需要把dist目录发布出去。 
  -  2.HTML中的script标签是自动引入的，所以可以确保资源文件的路径是正常的
- **HotModuleReplacementPlugin**：热加载，HMR 作为一个 Webpack 内置的功能

### 8、Rollup和webpack区别

- rollup所有资源放同一个地方，一次性加载利用tree-shake特性来剔除未使用的代码，减少冗余，可以将你的 js 中的代码，编译成你想要的格式
- webpack拆分代码、按需加载，但是webpack5已结默认带tree-shaking了，静态资源导入（如 js、css、图片、字体等）,拥有如此强大的功能，所以 webpack 在进行资源打包的时候，就会产生很多冗余的代码。

### 9、sourceMap

- source map 是将编译、打包、压缩后的代码映射回源代码的过程。
- map文件 只要不打开开发者工具、浏览器就不会加载。

### 10、热更新

webpack的热更新又称热替换简称HMR(HotModuleReplacementPlugin)。这个机制可以做到不用刷新浏览器而将新变更的模块替换掉旧的模块。

HMR 核心是：客户端从服务端拉取更新后的文件，准确的说是chunk diff（比较出需要更新的部分）。实际上，WDS（webpack-dev-server）与浏览器之间维护了一个 websocket，当本地资源发生变化时，WDS向浏览器推送更新，并带上构建时的hash，让客户端与上一次资源进行对比。客户端比对出差异好会向WDS发起Ajax请求来获取更改内容，这样客户端就可以再次借助这些信息继续向WDS发起jsonp请求，获取该chunk的增量更新。

### 11、chunk 分包

chunk：用于组织输出结构的对象，webpack分析完所有模块资源的内容，构建出完整的依赖图`Dependency Graph`之后，会根据用户配置以及依赖图的内容构建出一个或多个chunk，每个chunk与最终输出的文件大致上是一一对应的。

webpack编译过程大致上分为四个阶段

1. 初始化阶段
2. 构建阶段 make
3. 生成阶段 seal
4. 写入阶段 emit

- Dependency Graph：在构建(make) 阶段，webpack 从 entry 出发根据模块间的引用关系(require/import) 逐步构建出模块依赖关系图(ModuleDependencyGraph），依赖关系图表达了模块与模块之间互相引用的先后次序，基于这种次序 webpack 就可以推断出模块运行之前需要先执行那些依赖模块，也就可以进一步推断出那些模块应该打包在一起，那些模块可以延后加载(异步执行)

webpack根据模块依赖图的内容开始组织分包——chunk对象。

默认的分包规则有：

- 同一个`entry`下的模块组织成一个chunk
- 异步模块单独组织为一个chunk
- `entry.runtime`单独组织成一个chunk (webpack5)：Webpack 执行完 `entry`、异步模块分包后，开始遍历 `entry` 配置判断是否带有 `runtime` 属性，如果有则创建以 `runtime` 值为名的 `Chunk`

后续会有如`SplitChunksPlugin`插件在`Chunk`系列对象上进一步拆解、优化，最终反映到输出上才会表现出复杂的分包结果。

#### 11.1 chunk的父子关系

由`entry`生成的`Chunk`之间相互孤立，没有必然的前后依赖关系，但异步生成的`Chunk`不同，假设一个文件有同步引用的文件，也有异步引用的文件，由于异步的文件会单独进行打包`(Async chunk )`，但是这里异步的chunk是有单向依赖关系的，在这种情况下引用者被称为`parent`,被引用者为`child`，同步引用的文件都会存放到`ChunkGroup._parents`，异步的会存放到`ChunkGroup._children`属性中

### 12、loader缓存

默认情况下 Webpack 会缓存 Loader 的执行结果直到资源或资源依赖发生变化，开发者需要对此有个基本的理解，必要时可以通过 `this.cachable` 显式声明不作缓存，例如：

```javascript
module.exports = function(source) {
  this.cacheable(false);
  // ...
  return output;
};
```

因为在 Loader 中执行的各种资源内容转译操作通常都是 CPU 密集型 —— 这放在单线程的 Node 场景下可能导致性能问题；又或者异步 Loader 会挂起后续的加载器队列直到异步 Loader 触发回调，稍微不注意就可能导致整个加载器链条的执行时间过长。

### 13、tree shaking 摇树优化

Tree-Shaking 是一种基于 ES Module 规范的 Dead Code Elimination 技术，它会在运行过程中静态分析模块之间的导入导出，确定 ESM 模块中哪些导出值未曾其它模块使用，并将其删除，以此实现打包产物的优化。

在 Webpack 中，启动 Tree Shaking 功能必须同时满足三个条件：

- 使用 ESM 规范编写模块代码
- 配置 `optimization.usedExports` 为 `true`，启动标记功能
- 启动代码优化功能，可以通过如下方式实现：
  - 配置 `mode = production`
  - 配置 `optimization.minimize = true`
  - 提供 `optimization.minimizer` 数组

```js
// webpack.config.js
module.exports = {
  entry: "./src/index",
  mode: "production",
  devtool: false,
  optimization: {
    usedExports: true,
  },
};
```

Webpack 中，Tree-shaking 的实现一是先**标记**出模块导出值中哪些没有被用过，二是使用 Terser 删掉这些没被用到的导出语句，大概步骤如下：

1. Make 阶段，收集模块导出变量并记录到模块依赖关系图 ModuleGraph 变量中
2. Seal 阶段，遍历依赖图`ModuleGraph`标记模块导出变量有没有被使用
3. 生成产物时，若变量没有被其它模块使用则删除对应的导出语句

也就是说，标记的效果就是删除没有被其它模块使用的导出语句。

- ps：标记功能需要配置 `optimization.usedExports = true` 开启

摇树优化的优化效果并不是那么完美，需要使用者有意识的优化代码结构

#### 13.1 避免无意义的赋值

```js
// m.js
export const bar = 'bar'
export const foo = 'foo'

// i.js
import {bar, foo} from "./m.js"
console.log(bar) // 后续有在使用bar
const f = foo; // 后续没有在使用foo
```

如上，假设`foo`在后续没有用到，就不要引用或者引用了不要进行赋值操作，本应是可以优化掉的代码段，但是因为一个无意义的赋值操作，不会被优化掉。

#### 13.2 使用 `#pure` 标注纯函数调用

```js
/*#__PURE__*/  foo('be removed');
```

上面这个代码是用于明确告诉 Webpack 该次函数调用并不会对上下文环境产生副作用，可以被优化删除掉。

#### 13.3 禁止 Babel 转译模块导入导出语句

Babel 是一个非常流行的 JavaScript 代码转换器，它能够将高版本的 JS 代码等价转译为兼容性更佳的低版本代码，使得前端开发者能够使用最新的语言特性开发出兼容旧版本浏览器的代码。

但 Babel 提供的部分功能特性会致使 Tree Shaking 功能失效，例如 Babel 可以将 `import/export` 风格的 ESM 语句等价转译为 CommonJS 风格的模块化语句，但该功能却导致 Webpack 无法对转译后的模块导入导出内容做静态分析

