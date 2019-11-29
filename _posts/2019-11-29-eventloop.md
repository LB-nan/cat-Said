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


### 6、nextTick

vue里的一个方法,做一个缓存机制。具体使用就默认会用了。

原理就是微任务。

判断当前环境是否支持promise并且是原生的方法，如果支持，就封装一层promise做微任务，然后把调用nextTick注册的方法存到一个数组里面去，当完成之后依次调用。

PS：nextTick源码还判断了一些其他的方法，这里不详细展开了，有兴趣去看一哈。

原理:

```
var nextTickArr = [];

function nextTick(num){ // 注册事件
  // some event
  nextTickArr.push(()=>{
    console.log(num)
  });
}

nextTick(1)
nextTick(2)
nextTick(3)
nextTick(4)


console.log('前面执行一堆事件');
 // 包装一层微任务，当之前的任务做完之后执行nextTick之前注册的事件
new Promise((resolve, reject)=>{
  resolve()
}).then(data=>{
  nextTickArr.forEach(fn=>fn());
})

console.log('还有一堆事件');

/*
  前面执行一堆事件
  还有一堆事件
  1
  2
  3
  4
*/
```

#### 6.1 判断一个方法是否为原生

控制台输入`alert`

```
  alert
  // ƒ alert() { [native code] }
```

判断原生方法就是判断`alert.toString`的值是否含有`native code`字符串。

ps：可以自己写一个方法，重写他的`toString`，返回一个`[native code]`就可以欺骗别人自己为原生方法。



