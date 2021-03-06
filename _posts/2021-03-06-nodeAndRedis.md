---
layout: post
title: node连接redis
categories: node
description: node连接redis
keywords: node, javascript, redis
---

node连接redis

### 1、下载安装redis

windows下载地址：`https://github.com/microsoftarchive/redis/releases/tag/win-3.0.504`；Mac下载就官网就好了,我这里是Windows。

下载`Redis-x64-3.0.504.msi`完成后就跟着指引安装。

安装完成后打开安装目录，把`redis.windows.conf`文件打开，修改`port 6378`,`requirepass 123456`

`cd`到安装目录下，打开命令行工具，输入

```
.\redis-server.exe .\redis.windows.conf
```

然后回车，redis数据库就创建出功了。

重新在当前目录下打开个`cmd`，输入`redis-cli -p 6378`回车连接刚刚创建好的数据库，下方会有`127.0.0.1:6378`,在后面尝试输入以下内容

输入密码

```
auth 123456
```

set一条数据

```
set a 1
```

get一下刚才set进去的数据

```
get a
```

### 2、启动项目

初始化一个项目

```js
npm init -y
```

安装以下依赖

```js
"dependencies": {
  "ioredis": "^4.23.0",
  "koa": "^2.13.1",
}
```

创建`/test/test-redis.js`

输入以下代码

```js
async function test(){
  const Redis = require('ioredis');

  const redis = new Redis({
    port: 6378,
    password: 123456
  })

  const keys = await redis.keys('*');

  console.log(keys)
}

test();
```

命令行工具执行`node .\test\test-redis.js`, 输出`['a']`，也就是上面`set`的`key`

在代码里操作的话可以

```js
await redis.set('c', 123);
await redis.set('c', 10, 123); // 设置过期时间 10毫秒
```

这样就设置好了，`get`同理。