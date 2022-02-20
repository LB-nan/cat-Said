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
  - 处理一个文件可以使用多个loader，loader的执行顺序和配置的顺序是相反的，即最后一个loader最先执行，第一个loader最后执行
  - 前面执行的loader的返回值是后一个loader的入参，最后执行的loader会返回此模块的JavaScript源码
- plugin是插件，在webpack运行的生命周期中会广播许多事件，plugin可以监听这些事件，在合适的时候通过webpack的钩子改变输出结果

plugin是针对webpack打包的过程，它不直接操作文件，而是基于事件机制工作，会监听 webpack 打包过程中的某些事件钩子，执行任务。plugin 比 loader 强大，通过plugin 可以访问 compliler 和 compilation 过程，通过钩子拦截 webpack 的执行。

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

