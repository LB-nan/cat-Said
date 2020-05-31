---
layout: post
title: react route
categories: javascript
description: 实现react route
keywords: JavaScript, react, route
---

手撸一个react router

### 1、实现原理

前端框架实现路由都是只有两种方式：hash(window.location.hash) & h5API(history)实现

hash就是浏览器URL后面的#号与后面的值，着重说一下`history`。

#### 1.1 history

`history`对象提供了操作浏览器会话历史的接口。

历史对象维护了一个`栈`结构，就是`先进后出`，栈有`push`和`pop`两种方法。

`history`对象对应的方法：

1. pushState：HTML5引入了`history.pushState`,`history.replaceState`两个方法，第一个是添加一条历史记录条目，每次`push`一条`history.length`+1，第二个是修改或替换一条条目，通常与`onpopstate`一起使用。
2. popState：`window.onpopstate`是popState在`window`对象上的事件处理程序。


`pushState`：
1. `pushState`会往`history`中写入一个对象，`history.length`+1，url改变，该索引`history`对应有一个`state`对象，这个时候若是点击浏览器的后退，便会触发`popstate`事件，将刚刚存入的数据对象读出
2. `pushState`会改变`history`
3. 每次使用时候会为该索引的`state`加入我们自定义数据
4. 每次我们会根据`state`的信息还原当前的view，于是用户点击后退便有了与浏览器后退前进一致的感受
5. `pushState`需要三个参数：状态对象，标题，URL(可选)
6. 调用history.pushState()或者history.replaceState()不会触发popstate事件，popstate事件只会在浏览器某些行为下触发，比如点击后退、前进按钮（或者在JavaScript中调用history.back(),history.forward(),history.go()方法）。


```js
// 自己注册的 onpushstate方法
window.onpushstate = function (event){
  console.log(event)
}

~(function(history){
  let pushState = history.pushState;
  // 重写history的pushState方法
  // 在调用history.pushState 的时候多调用一个我们的回调，方便进行监听
  history.pushState = function (state, title, pathname){
    if(typeof window.onpushstate == 'function'){
      window.onpushstate({state, pathname, title, type: 'pushstate'})
    }
    return pushState.apply(history, arguments);
  }

})(window.history)

setTimeout(()=>{
  history.pushState({page:1}, 'page1', '/page1')
}, 3000)

```

### 2、router的使用

入口文件的代码：

```js
import React from 'react';
import ReactDOM from 'react-dom';
import { HashRouter as Router, Route } from 'react-router-dom'

import Home from './components/Home'
import User from './components/User'

let root = document.getElementById('root');

ReactDOM.render(
  <Router>
    <>
      <Route path="/" component={Home}  />
      <Route path="/User" component={User}  />
    </>
  </Router>,
  root
)
```

Home和User内部代码基本上一致：
```js
import React from 'react';
// RouteComponentProps 是定义好的类型，是props的值对应的类型
import { RouteComponentProps } from 'react-router-dom';

type Props = RouteComponentProps & {
 // 这里可以写我们自定义的类型
}

// <Props>: 如果上面不引用RouteComponentProps然后也不定义这个的话，在里面是拿不到this.props.location的，只能拿到this.props，因为默认只有一个props，然后props有一个children，类型是一个reactElement，所以需要定义好，然后才可以使用

// this.props返回了三个对象
// 1. history: 历史对象，不是window.history，是内部的一个实现
// 2. location：当前路径的一些信息，与history.location一致
// 3. match：匹配对象
export default class extends React.Component<Props> {
  render() {
    console.log(this.props.location)
    return (
      <div>User</div>
    )
  }
}

```






