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

```js
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

```js
  alert
  // ƒ alert() { [native code] }
```

判断原生方法就是判断`alert.toString`的值是否含有`native code`字符串。

ps：可以自己写一个方法，重写他的`toString`，返回一个`[native code]`就可以欺骗别人自己为原生方法。


### 7、node事件环

node有6个宏任务队列，2个微任务。其中宏任务队列有两个系统内置任务(I/O callback、idle，prepare)、一个关闭事件的回调不予考虑。其他3个任务队列分别为`timer、poll、check`排名分先后。


#### 7.1 三个宏任务

三个宏任务排名虽然分先后但是也不一定真的就那样。

1. timer：timer就是指`setTimeout setInterval`，位于宏任务第一队列，但是只有他们的时间到了之后才会被加入到队列里面去。
2. poll：poll指的是I/O事件，比如`fs.readFile`, 位于宏任务第二队列。
3. check：check指的是`setImmdiate()`，这个方法浏览器端只有IE才有，node自己也实现了一个，这个方法不需要写时间，会在I/O宏任务执行完毕之后立即执行，位于宏任务第三队列。
4. 微任务(跑错片场)：每次主线程执行完毕之后都会清空微任务，然后从三个队列里执行宏任务，和浏览器一致。


#### 7.2 执行顺序

1. 主线程执行完毕之后，清空微任务。 （node中的nextTick() 要比Promise.then()快）
2. 然后去`timer`队列里执行宏任务，如果有微任务注册到微任务队列，执行完毕当前宏任务队列之后清空微任务。
3. 第一队列的宏任务执行完毕之后，看`I/O`队列是否有任务，有的话立即执行，如果期间有微任务同上的处理办法，如果有`setTimerout`类似的任务会等定时器到时间之后注册到`timer`队列里面去
4. 第二队列`I/O`执行完毕查看第三队列是否有宏任务，如果有立即执行，如果没有就重新查找`timer`队列。
5. 如果先执行的是`I/O`宏任务，poll宏任务里面有定时器timer宏任务，同时下面还有`check`宏任务，则立即执行`check`宏任务，然后回头执行`timer`宏任务。



#### 7.3 代码验证

以后可能因为node版本升级导致会有略微差异,当前node版本`v10.16.3`

```js
  let fs = require('fs');

  fs.readFile('./name.txt', 'utf8',function(err) {
    // then 微任务
    Promise.resolve().then(()=>{
      console.log('promise.then微任务1')
    })

    // timer宏任务
    setTimeout(()=>{
      console.log('timer宏任务1')
      
      // then 微任务
      Promise.resolve().then(()=>{
        console.log('promise.then微任务2')
      })
      
      // nextTick 微任务
      process.nextTick(()=>{
        console.log('next微任务')
      })
    },0)

    // poll 宏任务
    console.log('poll宏任务')

    // timer宏任务
    setTimeout(()=>{
      console.log('timer宏任务2')
    },0)

    // check宏任务
    setImmediate(()=>{
      console.log('imm宏任务')
      // then 微任务
      Promise.resolve().then(()=>{
        console.log('promise.then微任务3')
      })
    })
  })

/*
  poll宏任务
  promise.then微任务1
  imm宏任务
  promise.then微任务3
  timer宏任务1
  timer宏任务2
  next微任务
  promise.then微任务2
*/

```




