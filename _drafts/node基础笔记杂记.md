---
layout: post
title: node基础
categories: noe
description: node
keywords: node
---

node基础

### 1、node

node主要的几个全局变量：process、setTimeout、setInterval、setImmediate


### 2、process

process：进程

有两个主要的属性

process.platform: 平台，可以用这个判断运行在什么平台上面。
process.argv： 代表用户传递的参数，默认前两个参数为node使用，没有意义，用户自己的参数需要从第三个开始，`node a.js --config xxxx`，使用`process.argv.slice(2)`接收。

处理参数的key和value

```
  let config = process.argv.slice(2).reduce((memo,current,index,array)=>{
      if(current.includes('--')){
          memo[current.slice(2)] = array[index+1];
      }
      return memo;
  },{});
```

#### 2.1 chdir

 process.chdir()方法变更 Node.js 进程的当前工作目录，如果变更目录失败会抛出异常

#### 2.2 cwd
 process.cwd()： 返回当前工作路径。
 `console.log(global.process.cwd())`;

#### 2.3 env
环境变量。用webpack应该比较熟悉这个。

`console.log(global.process.env)`

#### 2.4 nextTick

```
  process.nextTick(()=>{ // node中的微任务
      console.log('nextTick')
  });
```

#### 2.5 扩展
commander： 命令行的管家 帮你提供--help ,必须先安装，这个配合`process.argv`使用。

```
const program = require('commander');
program.command('create');
program.version('1.0.0');
program.on('--help',()=>{
    console.log('别help，去百度'))
}).parse(process.argv);

```

chalk：粉笔， 给命令行输入的文本加颜色的。

```
const chalk = require('chalk');
program.on('--help',()=>{
    console.log(chalk.red('别help，去百度'));
}).parse(process.argv);
```


### util 工具类

node的一个内置工具， 有两个主要方法`util.promisify(fn)`把函数转换成promise,`util.inherits`实现继承，不是用ES6写的，用的是类的继承实现的；它还有各种类型判断的方法。


### events 事件模块

node的事件绑定核心方法：`on`注册事件、 `emit`触发事件、 `once`只执行一次的事件、; `newListener`监听某个事件调用、`off`注销某个事件。





