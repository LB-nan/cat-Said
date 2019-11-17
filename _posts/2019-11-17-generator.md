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


