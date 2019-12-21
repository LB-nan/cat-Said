---
layout: post
title: node server
categories: node
description: node
keywords: node, npm, nodeServer
---

创建一个node服务器

### 1、初始化项目

使用`npm init -y` 初始化一个项目，根目录配置创建一个执行文件夹`bin`，在`bin`下创建一个文件`www`，在`www`里面输入
```
#! /usr/bin/env node
console.log('aaa');
```

配置脚本，在`package.json`里面新建一个字段`bin`，配置如下
```
{
  "name": "my-server",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "bin": {
    "server": "./bin/www"
  },
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```


然后我们打开终端，进入根目录，映射到全局作用域下。
```
npm link
```

映射完成之后终端输入`server`, 打印`aaa`。


### 2、配置Babel
把ES6的模块化转为符合cjs规范，使用Babel处理。

安装`"@babel/cli`,`@babel/core`,`@babel/preset-env`, `commander`。

根目录新建一个`.babelrc`，输入配置
```
{
  "presets": [
    ["@babel/preset-env", {
      "targets": {
        "node": "current"
      }
    }]
  ]
}
```

配置脚本,在`package.json`的`scripts`字段下
```
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "babel": "babel src -d dist --watch"
  }
```

### 3、配置入口文件
新建一个`src/main.js`，在`bin/www`里面输入`require('../dist/main');`,因为我们要引用打包后的，配置了打包到`dist`目录，所以直接引用这个目录。

在`main.js`里面输入
```
import program from 'commander';
```

然后运行：`npm run babel:watch`,看`dist`目录已经打包出来文件了。 然后我们开始正式创建服务器。

### 4、创建服务器

我们创建一个`src/server.js`，在`main.js`里面引用`import Server from './server'`;

在`main.js`输入下面内容，把配置传进去。

```
let config = {
      port: 8080
}

Object.assign(config, program);

let server = new Server(config);
server.listen(); // 启动服务
```

