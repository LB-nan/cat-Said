---
layout: post
title: generator生成器
categories: javascript
description: promise
keywords: JavaScript, generator, async-await
---


ES6 generator生成器 与 async-await


### 1、generator生成器

生成器会返回一个迭代器。
迭代器：是有next方法，而且方法执行后，需要返回 `value, done`；
如果迭代器走完，done的值为true，否则为false。

迭代器每次运行都会到`yield`处卡住，并且这一次的参数会赋值上一次`yield`的赋值的地方，所以第一次赋值是无效的。

用法：

```
  function * fn(){
    let a = yield 1;
    console.log(a)
    let b = yield 2;
    console.log(b)
    let c = yield 3;
    console.log(c)
  } 
  let fn1 = fn();
  console.log(fn1.next(2)); // { value: 1, done: false } 
  console.log(fn1.next(3)); // 3 { value: 2, done: false }
  console.log(fn1.next(4)); // 4 { value: 3, done: false }
  console.log(fn1.next(5)); // 5 { value: undefined, done: true }
```

### 2、async-await

上面的代码调用起来是不是很麻烦，疯狂next。。我们可以用`async-await`来解决，它的原理同样是generator。

```
  const fs = require('fs').promises;
  // async + await 其实是 generator + co的语法糖
  async function  read(){ // async函数返回的是promise
      try{
          let content =  await fs.readFile('./name.txt','utf8'); // nnnnn
          return content;
      }catch(e){
          console.log(e);
      }
  }
  read().then(data=>{
      console.log(data); // nnnnn
  },err=>{
      console.log(err);
  })
```


#### 2.1 co原理

```
  function co(gen){
    return new Promise((resolve,reject)=>{
        // 异步迭代需要先提供一个next方法, 返回一个对象，有value 和 done
        function next(data){
            let {value,done} = gen.next(data);
            if(!done){
                // 可能是一个普通值，包装为promise
                Promise.resolve(value).then(data=>{
                    next(data);
                },err=>{
                    reject(err);
                })
            }else{
                resolve(value);
            }
        }
        next();
    })
  }
```


