---
layout: post
title: 前端基础巩固扩展
categories: javascript
description: 单元测试知识点整理
keywords: Web, Dom, CSS, JavaScript
---

前端知识点小记。

### 1、简介

记录一些课程的主要内容，以及部分自己的知识理解。

### 2、标签

主要记录一些非常重要却容易被忽视的标签。

#### 2.1 mate标签

假设要实现一个类似 PPT 自动播放的效果，可以通过mate标签来实现。

```js
<meta http-equiv="Refresh" content="5; URL=page2.html">
```

上面的代码会在 5s 之后自动跳转到同域下的 page2.html 页面。

#### 2.2 script标签

浏览器的解析机制：在渲染解析HTML的过程中，如果遇到script标签引用文件，会暂停解析HTML，然后去下载js文件，文件下载完成后执行，执行完成后再继续渲染HTML。

这个机制就导致`script`标签会阻塞渲染过程，延长了用户看到页面的时间，降低了用户体验。

可以通过`script`标签的属性来规避这个问题。

1. `async`属性：立即请求文件，但不阻塞渲染引擎，而是文件加载完毕后阻塞渲染引擎并立即执行文件内容。
2. `defer`属性：立即请求文件，但不阻塞渲染引擎，等到解析完 HTML 之后再执行文件内容。
3. HTML5 标准`type`属性：对应值为“module”。让浏览器按照 ECMA Script 6 标准将文件当作模块进行解析，默认阻塞效果同 defer，也可以配合 async 在请求完成后立即执行。

除此之外还应当注意，当渲染引擎解析 HTML 遇到 script 标签引入文件时，会立即进行一次渲染。所以这也就是为什么构建工具会把编译好的引用 JavaScript 代码的 script 标签放入到 body 标签底部，因为当渲染引擎执行到 body 底部时会先将已解析的内容渲染出来，然后再去请求相应的 JavaScript 文件。如果是内联脚本（即不通过 src 属性引用外部脚本文件直接在 HTML 编写 JavaScript 代码的形式），渲染引擎则不会渲染。

#### 2.3 link标签

合理利用link标签的`rel`属性值来进行预加载，能进一步提升渲染速度。

1. `dns-prefetch`：当 link 标签的 rel 属性值为“dns-prefetch”时，浏览器会对某个域名预先进行 DNS 解析并缓存。这样，当浏览器在请求同域名资源的时候，能省去从域名查询 IP 的过程，从而减少时间损耗。
2. `preconnect`：让浏览器在一个HTTP请求正式发送给服务器前预先执行一些操作，包括DNS 解析、TLS 协商、TCP 握手，消除往返延迟的时间。
3. `prefetch/preload`：两个值都是让浏览器预先下载并缓存某个资源，但不同的是，prefetch 可能会在浏览器忙时被忽略，而 preload 则是一定会被预先下载。
4. `prerender`：浏览器不仅会预加载资料，还会解析执行页面，进行预渲染。

### 3、DOM

DOM：Document Object Model，文档对象模型

#### 3.1 为什么说DOM操作耗时

- 如果你对浏览器结构有一定了解，就会知道浏览器包含渲染引擎（也称浏览器内核）和 JavaScript 引擎，它们都是单线程运行。单线程的优势是开发方便，避免多线程下的死锁、竞争等问题，劣势是失去了并发能力。

- 浏览器为了避免两个引擎同时修改页面而造成渲染结果不一致的情况，增加了另外一个机制，这两个引擎具有互斥性，也就是说在某个时刻只有一个引擎在运行，另一个引擎会被阻塞。操作系统在进行线程切换的时候需要保存上一个线程执行时的状态信息并读取下一个线程的状态信息，俗称上下文切换。而这个操作相对而言是比较耗时的。

- 每次 DOM 操作就会引发线程的上下文切换——从 JavaScript 引擎切换到渲染引擎执行对应操作，然后再切换回 JavaScript 引擎继续执行，这就带来了性能损耗。单次切换消耗的时间是非常少的，但是如果频繁地大量切换，那么就会产生性能问题。

#### 3.2 重排（Reflow）与重绘（Repaint）

1. 重排：修改元素的盒模型或者改了浏览器的DOM节点或者视口宽高会触发重排。
2. 重绘：修改了元素的颜色之类的不会影响计算的属性只会发生重绘。

#### 3.3 如何高效操作DOM

老生常谈的话题，根据以上原因就可以找到方案

1. 尽量不要使用复杂的匹配规则和复杂的样式，从而减少渲染引擎计算样式规则生成 CSSOM 树的时间；
2. 尽量减少重排和重绘影响的区域；
3. 使用 CSS3 特性来实现动画效果。

### 4、页面渲染

我们请求一个网页就是请求数据，数据在网络传输中都是以字节的形式流转，然后浏览器再对数据进行解析处理。

解析分为两步：

1. 将字符数据转化为Token
2. 解析HTML生成DOM树

生成DOM树后渲染的过程中，解析HTML标签，生成DOM树，然后解析CSSOM树，俩树生成渲染树，生成渲染树的过程是从DOM树找节点然后去CSSOM树自下而上进行匹配`(因为CSS的匹配规则是自右向左，所以DOM是从下往上，如果从上往下会重复渲染)`，生成渲染树的过程会自动忽略一些不需要立刻展示的节点，比如`display:none;`同时也会将需要加的伪类添加到渲染树里面；生成渲染树之后就开始布局，根据渲染树生成的规则进行布局，布局完成然后进行绘制。绘制就是把渲染树的每个节点生成屏幕上真实的像素。

### 5、JavaScript数据类型

老生常谈的话题，不重复记录。记录一下重点。

js的精度问题是js的诟病，类似于`0.1+ 0.2 != 0.3`这种问题，解决方法通常就舍弃调末尾的小数位。

```js
parseFloat((0.1 + 0.2).toPrecision(12)) // 0.3
```

js的`Number`类型还有两个特殊数值需要注意

1. NaN（Not a Number）通常在计算失败的时候会得到该值。要判断一个变量是否为 NaN，则可以通过 Number.isNaN 函数进行判断。
2. Infinity 是无穷大，加上负号 “-” 会变成无穷小，在某些场景下比较有用，比如通过数值来表示权重或者优先级，Infinity 可以表示最高优先级或最大权重。

`Number`的进制转换，js默认是10进制，其他进制转换成10进制可以使用`parseInt()`函数，第一个参数为数值或者字符串，第二个参数为进制数，默认是10；将10进制转换成其他进制可以使用`toString()`来实现,比如：`(10).toString(2); // 1010`。

### 6、this

说到js，`this`肯定是绕不过去的问题，`this`的用法一句话概括：

- 谁调用`this`,`this`指向谁，没有人调用指向全局。

this的指向修改可以通过`bind\apply\call`。

### 7、原型

讲js的对象，总是说`js里万物皆对象`，其实说的就是原型，绕来绕去最后的原型都是`Object`。`原型`是`JavaScript`最重要的特效之一，可以让对象从其他对象继承属性方法。

说原型就要提原型链

- 原型链：对象通过自身的`__proto__`来查找自身继承的父类的一个机制被称作原型链，源头是`Object`。

知道对象，那么怎么创建对象，创建对象可以`new`一个对象，身高体重都可以自行配置；还可以直接赋值成对象。直接创建有一个相对比较特殊的方法`Object.create()`，不传参创建一个`空`对象，这个`空`是指原型没有指向其他对象，直接指向`Object`原型，如果传入一个对象，则新对象继承传入的对象，构成一节原型链。

```js
function Girl(){}
new Girl({...});

let a = {};

let b = Object.create();

```

那么`new`的过程中发生了什么

1. 创建了一个临时空对象，指向`构造函数`的原型。(被New的函数称为构造函数，默认首字母大写`Girl`)
2. 执行构造函数，将this指向新对象，并传入参数，返回一个值。
3. 判断返回值为非空对象，则返回返回值，否则返回新对象。

#### 7.1 typeof 和 instanceof

`typeof`返回的是对象的基本类型，所有的对象类型都返回`object`，包括特殊的`null`。

`instanceof`用于检测构造函数的 prototype 属性是否出现在某个实例对象的原型链上。

### 8、作用域

作用域是指赋值、取值操作的执行范围，通过作用域机制可以有效地防止变量、函数的重复定义，以及控制它们的可访问性。在js里面作用域经常一起被提及的问题就是`变量提升`。对于使用`var`关键字声明的变量以及创建命名函数的时候，JavaScript 在解释执行的时候都会将其声明内容提升到作用域顶部，这种机制称为`命名提升`。作用域在实际代码中常出现的场景是闭包的使用。`闭包：在函数内部访问外部函数作用域时就会产生闭包。`闭包很有用，因为它允许将函数与其所操作的某些数据（环境）关联起来。这种关联不只是跨作用域引用，也可以实现数据与函数的隔离。

### 9、执行顺序

前端的代码里面很多事情都要考虑`执行顺序`，从浏览器加载HTML代码的顺序到js的执行顺序。

主要说js的顺序，见文章`https://catsaid.cn/2019/11/29/eventloop/`。

### 10、总结

其实没有了，就是想凑个整。本篇记录一下单纯js的知识点内容，回顾一下知识点，串一下知识脉络，一些常识性或者细节没有展开写，网上很多内容，记录个大概。
