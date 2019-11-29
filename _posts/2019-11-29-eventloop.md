---
layout: post
title: eventloop
categories: javascript
description: 事件环
keywords: JavaScript, eventloop, 事件环, 宏任务, 微任务
---

浏览器事件环&node事件环


### 1、事件环

事件环就是代码的执行顺序，JS进程中主线程执行完毕后会走异步代码，因为JS是单线程，所以进程中只有一条线程，js的异步事件会再次开辟一条线程不会马上执行( 不是主线程 )，叫做任务队列，任务队列有宏任务与微任务之分。

宏任务每次取一个，微任务每次取一组。

### 2、宏任务队列

等主线程全部执行完毕，清空微任务队列之后，会取出宏任务队列里的第一个宏任务放到主线程里面执行，如果执行过程中遇到微任务会把他添加到微任务队列，执行完当前宏任务后立即执行微任务队列的任务，然后再去宏任务队列里取下一个宏任务。执行过程中遇到宏任务会放到宏任务队列的最后一个位置等待执行。

js的宏任务：script、Ajax、事件、requestFrameAnimation、定时器。

node的宏任务：I/O 视图渲染。

### 3、微任务队列

微任务会比宏任务快，但是`script`例外，因为js是script脚本，所以会先执行`script`。

微任务：promise.then(promise的参数函数是立即执行的)、 MutationObserver、process.nextTick


### 4、进程

进程里包含着线程。JS是单线程，所以进程里只有一个主线程。


### 5、线程

线程是计算机任务的最小单位。




