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

具体可见GitHub项目<https://github.com/oldpubcat/http-server>


### 5、gzip压缩

- 优点：对JS，CSS, HTML或字符串重复率较高的资源的压缩率很高，可以极大得提高文件传输的速率，从而提升web性能
- 缺点：服务端要对文件进行压缩，而客户端要进行解压，增加了两边的负载，以服务器性能换浏览器的时间。浏览器解析一个压缩文件的速度很快，只需要考虑服务器的性能能不能扛得住。

1. node内置了一个模块叫`zlib`，`zlib` 模块可以用来实现对 HTTP 中定义的 gzip 和 deflate 内容编码机制的支持。
2. `gzip`压缩是提取相同的字符串进行替换，重复内容越多压缩率越高。
3. 如下使用

```js
const zlib = require('zlib');
const fs = require('fs');
const gzip = zlib.createGzip();
const deflate = zlib.createDeflate();
const rs = fs.createReadStream('input.txt');
const ws = fs.createWriteStream('input.txt.gz');
rs.pipe(gzip).pipe(ws);
```

#### 5.1 webpack中开启gzip压缩

可以使用插件`compression-webpack-plugin`。

```js

  // webpack.config.js
  const CompressionPlugin = require("compression-webpack-plugin");
  {
    ...,
    plugins:[
      new CompressionPlugin({
         algorithm: 'gzip'
      })
    ]
  }
```

通过webpack配置：`devServer.compress`:

- 这是一个布尔型的值，当它被设置为true的时候对所有的服务器资源采用gzip压缩

```js
devServer:{
  compress: true,
  ...
}
```


