---
layout: post
title: react
categories: react
description: react
keywords: react
---

### 1、jsx

jsx本质上就是js代码，jsx是写结构的语法糖，会被Babel工具转变为JavaScript代码。

在jsx相关使用
1. 可以使用表达式，比如三目，模板字符串，或者简单的数学运算，函数执行等，基本上和vue的差不多
2. 根节点只能有一个，如果需要定义一组节点，还不想定义一个父节点包裹，可以使用`<Fragment></Fragment>`包裹，就不会生成了。 `import React, {Fragment} from 'react';`
3. 可以嵌套
4. 设置属性，比如`style`，`<p style={{color: red;}}></p>`.另外class需要写成`className`,连接属性要改成驼峰的，比如`marginLeft`



### 2、组件

react声明组件有两种方式，要么通过函数声明，要么通过类声明组件。


#### 2.1 函数组件

```js
import React from 'react';
import ReactDOM from 'react-dom';

ReactDOM.render(
  <div>
    你好，react
    <People />
  </div>,
  document.getElementById('root')
)

function People(){
  return (
    <div>peoplddd</div>
  )
}
```



#### 2.2 类组件

class声明的类组建，需要放到render上面，因为js的规则，function是可以的。

```js
import React from 'react';
import ReactDOM from 'react-dom';

class Man extends React.Component {
  render() {
    return (
      <div>mannnnn</div>
    )
  }
}

ReactDOM.render(
  <div>
    你好，react
    <Man/>
  </div>,
  document.getElementById('root')
)

```

### 3、组件传值

使用props来传递，和vue类似。不同的是，函数式组件接受数据是用传参，类组件是用this

#### 3.1 函数式传参

使用第一个参数来接受

```js
function People(props){
  return (
    <div>{props.name}</div>
  )
}

ReactDOM.render(
  <div>
    你好，react
    <People name='cat'/>
  </div>,
  document.getElementById('root')
)
```

还可以传其他值
```js
 <People name='cat' number={666}  boo={true}  fn={fn => 1}/>
```

传递子组件
```js

function People(props){
  return (
  <div>
    {props.name}
    {props.children}
  </div>
  )
}

ReactDOM.render(
  <div>
    你好，react
    <People name='cat' children={<div>我是children</div>}/>
  </div>,
  document.getElementById('root')
)
```

或者这样也可以
```js
ReactDOM.render(
  <div>
    你好，react
    <People name='cat' >
      <div>我是children</div>
    </People>
    <Man name="said"/>
  </div>,
  document.getElementById('root')
)
```


#### 3.2 类组件传参

类组件使用实例接受，也就是用this

```js
class Man extends React.Component {
  render() {
    return (
    <div>{this.props.name}</div>
    )
  }
}

ReactDOM.render(
  <div>
    你好，react
    <People name='cat'/>
    <Man name="said"/>
  </div>,
  document.getElementById('root')
)
```

方便起见，我们改成这样
```js
class Man extends React.Component {
  render() {
    let {name} = this.props;
    return (
    <div>{name}</div>
    )
  }
}

```


#### 3.3 设置默认值

设置组件的默认值使用`defaultProps`
```js
function People(props){
  console.log(props)
  return (
  <div>
    {props.name} age:{props.age}
    {props.children}
  </div>
  )
}

People.defaultProps = {
  age: 20
}
```

