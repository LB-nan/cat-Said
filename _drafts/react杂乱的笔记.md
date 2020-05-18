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



#### 3.4 对传参进行类型检查

检查组件参数的类型使用`prop-types`

```js
import PT from 'prop-types'
function People(props){
  return (
  <div>
    {props.name} age:{props.age}
    {props.children}
  </div>
  )

People.defaultProps = {
  age: 20
}
People.propTypes = {
  name: PT.string
}
ReactDOM.render(
  <div>
    你好，react
    <People name={20} >
      <div>我是children</div>
    </People>
    <Man name="said"/>
  </div>,
  document.getElementById('root')
)
```

这样就成功的报警告了：Warning: Failed prop type: Invalid prop `name` of type `number` supplied to `People`, expected `string`in People`

`prop-types`的值有下面几种

1. PT.array
2. bool
3. number
4. object
5. string
6. symbol
7. node
8. element

如果需要设置必填，可以设置`isRequired`
```js
PT.string.isRequired
```

### 4、组件的内部状态 state

只有类组件才有内部状态，其实这个和vue的`data`类似。vue是数据驱动，react更多的是状态驱动，每一次修改state对于react都是状态改变，然后去修改值，每次状态改变都会比较新旧的virtual DOM 的结果，对变化的部分进行更新。

修改state的值， 这个和微信小程序的写法相似
```js
this.setState({
  name: 'new name'
})
```

之所以说state是内部状态是因为state完全受控于当前组件，其他组件的state修改也不会影响到当前组件的state。

在一次事件执行期间的所有`setState`操作会合并成一次操作，然后更新`render`函数。所以在当前事件里获取到的state的值都是改变之前的值。

```js
// state =  {a : 1};

onClock(()=>{
  this.setSatet({
    a: 2
  })
  console.log(this.state.a) // 1
})

```

当你想要获取你当前修改的值，可以使用函数式写法，给`setState`传入一个函数，它接收两个参数，第一个是你传入的参数，第二个是props,需要有一个返回值，返回你要修改的对象。

```js
// 当前组件叫 Man
<div onClick={()=>{
  this.setState({
    name: '猫'
  }})

  this.setState((prev, props)=>{
    // 这里获取的是上面设置的值
    console.log(prev, props)  // {name: "ccccat"}  {name: "said"}
    return {
      name: '又修改了一次'
    }
  })
}}></div>


// 使用的地方
 <Man name="said"/>
```


### 5、事件

关于事件的写法上面也已经有了，DOM原生事件会接收一个`event`参数，在react里面也有，只不过这个是一个合成对象，react对这个进行处理了。

```js
<div onClick={(event)=>{
  console.log(event);
}}>{name}</div>
```

可以使用event进行阻止冒泡`event.stopPropagation()`或者阻止默认行为`event.preventDefault()`。

react支持的事件及事件对象更多看文档。


react事件方面最大的问题可能就是this了，this其实是JavaScript老生常谈的话题了，在行内绑定事件的时候如果没有用箭头函数，会找不到this，需要使用`bind`绑定以下this到当前实例，最好用箭头函数就好了。

在行内绑定this会有一个问题，就是由于组件每次会重新渲染，然后事件都会被重新注册，本来是没必要重新替换的，由于绑定了this，会重新返回一个事件，react会以为需要重新渲染，多多少少会有一些浪费性能，我们可以在`constructor`里面进行绑定

```js
this.handleEvent = this.handleEvent.bind(this);
```

最好的方案是使用箭头函数或者官网推荐的类属性语法。

```js
this.handleEvent = () => {
  // sth
}
```

### 6、生命周期函数

装载阶段：

  1. `constructor()`：
  2. `static getDerivedStateFromProps()`：在render之前可修改state的函数，不改变就返回null
  3. `render()`：渲染虚拟DOM
  4. `componentDidMount()`：DOM挂载完成

更新阶段：

  1. `static getDerivedStateFromProps()`：父组件更新，子组件可以使用这个进行监控更新
  2. `shouldComponentUpdate(nextProp, nextState)`：用于性能优化，返回一个true组件正常更新，返回false，视图不会更新。
  3. `render()`
  4. `getSnapshptBeforeUpdate(prevProp, prveState)`：需要配合`componentDidUpdate`一起使用，获取一个更新之前的快照。它执行的时候已经计算好新的virtual DOM，但是浏览器还未更新DOM。
  5. `componentDidUpdate(p, s, shot)`：更新已完成的回调，第三个参数是`getSnapshptBeforeUpdate`的返回值。

卸载阶段：

  1. `componentWillUnmount()`:


错误处理：

  1. componentDidCatch(error, info): 捕获子组件的生命周期函数内抛出的错误。


### 7、获取DOM

想要获取真实DOM节点，需要在组件render之后，然后才可以在`componentDidMount`函数中拿到。

方式一：`ref`

```js
<div ref= {(el) => {console.log(el)}}></div>
```

方式二：`createRef()`

```js
import React, {createRef} from 'react';
constructor(props){
  this.div1 = createRef();
}
render(){
  return (
    <div ref= {this.div1}></div>
  )
}
```

### 8、受控组件

当插入一个表单组件，例如`input`，渲染完成后输入会正常输入，由于在react中，组件的变化由状态变化导致的，而现在状态没有变，这个input不受react控制，这种组件叫做非受控组件，也就是说它的状态变化不受react控制的，如果想控制它，就需要给`input`加一个`value`值，一旦加了`value`值，只要react不改变`value`，input永远不会改变。

```js
<input value=""></input>
```

监控了input之后的处理
```js
constructor(props){
  super(props)
  this.state =  {
    name: 'ccccat',
    val: ''
  }
}

changeInput = (e) => {
  let val = e.target.value;
  console.log(val)
  this.setState({
    val: val
  })
}

render() {
  let {name, val} = this.state;
  return (
    <input value={val} onChange={this.changeInput}></input>
  )
}
```






