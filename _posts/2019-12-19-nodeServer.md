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
```js
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
```js
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
```js
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "babel": "babel src -d dist --watch"
  }
```

### 3、配置入口文件
新建一个`src/main.js`，在`bin/www`里面输入`require('../dist/main');`,因为我们要引用打包后的，配置了打包到`dist`目录，所以直接引用这个目录。

在`main.js`里面输入
```js
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

### 6、缓存
使用缓存可以降低服务器压力，减少冗余的请求，服务器可以快速的响应。服务器设置缓存有`强缓存`与`协商缓存`两种。

#### 6.1 强缓存

浏览器中的缓存作用分为两种情况，一种是需要发送HTTP请求，一种是不需要发送。首先是检查强缓存，这个阶段不需要发送HTTP请求。

设置强缓存后，网站首页不会被缓存，访问的其他页面或资源如果有强缓存，不会发起请求。

检查强缓存看的是过期时长是否已经到达，对应的字段是`max-age`;当后台设置了该值`res.setHeader("Cache-Control", "max-age=3600");`,代表了该缓存的时间为一小时，不到一个小时内所有请求都直接走缓存。

两种写法：新浏览器设置`Cache-Control`就好了。
```js
res.setHeader("Cache-Control", "max-age=3600");
res.setHeader("Expires", new Date(Date.now() + 3600 * 1000).toGMTString());
```

缺陷：如果页面更新频繁，设置强缓存后，页面更新会有延后。可以设置`对比缓存(协商缓存)`
```js
res.setHeader("Cache-Control", "no-cache"); // 代表每次都访问服务器，但是缓存
res.setHeader("Cache-Control", "no-store"); // 不进行缓存。
```


超过一个小时后，强缓存失效了，然后就要去走`协商缓存`了。


#### 6.2 协商缓存

强缓存失效之后就发送请求，浏览器在请求头中携带相应的缓存`tag`来向服务器发请求来决定是否使用缓存，这就是协商缓存。 

这样的缓存tag分为两种: `Last-Modified：最后修改时间` 和 `ETag：文件唯一标识/指纹`。这两者各有优劣，并不存在谁对谁有绝对的优势，跟上面强缓存的两个 tag 不一样。

设置`Last-Modified`，服务器传一个时间戳给浏览器进行缓存，浏览器再次请求服务器的时候带着这个时间戳，字段为`if-modified-since`，服务器进行对比，如果没有改变，则返回`304`，去缓存里面寻找。

`Last-modified`不是很靠谱，主要有以下三种情况。
1. 如果文件没有发生变化，但是进行操作了，比如修改后又恢复，这样修改时间就变了
2. 一秒内改变多次，`Last-modified`是无法监控到秒内修改的.
3. CDN分发，可能时间是不一致的。

这种情况可以使用`ETag`来做缓存处理。

`Etag` 文件指纹必定用md5，所以使用`crypto`这个模块

```js
import crypto from 'crypto';
let Etag = crypto.createHash('md5').update(fs.readFileSync(filePath)).digest('base64');
res.setHeader("Etag", Etag);
// 获取客户端请求携带的Etag指纹
let ifNoneMatch = req.headers['if-none-match'];
// 如果存在且相等，说明有缓存
if(ifNoneMatch && ifNoneMatch === Etag){
  return true;
}
```

#### 6.3 两者异同

都可以进行缓存，`Last-Modified`更高效，因为只需要一个时间点，`ETag`更精准，因为需要看文件内容生成hash值，如果两者皆存在，优先用`Etag`。

具体参考<https://juejin.im/post/5df5bcea6fb9a016091def69>

