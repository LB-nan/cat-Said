---
layout: post
title: typescript
categories: typescript
description: typescript学习笔记
keywords: typescript
---

typescript学习笔记，跟着学习记录，不断完善。

### 1、typescript简介

> 一个开源的语言。

> typescript是基于ECMAScript进行拓展，JavaScript的超集。

> typescript可以编译成可读的，符合规范的JavaScript。

> typescript是强类型语言。

> 是属于微软的，所以推荐用VSCODE编辑器

> 免费开源，使用apache授权协议

>  添加了可选静态类型、类、模块。

> 是一款跨平台的工具，支持所有的浏览器，主机和操作系统。

> 编译时检查，不污染运行时。 

安装：`npm install -g typescript`

使用：

> 转换单个文件: `tsc tsFileName.ts`  

> 多个文件：`tsc --init`
> 生成了一个`tscconfig.json`的文件
> 然后只需要输入`tsc`命令就好了
> 使用tsc命令会在终端看到报错的changelog

> 插件，vscode `TypeScript Auto Compiler`
> `tscconfig.json`初始化一个config文件，然后修改ts文件的时候保存完自动转换为js文件了。


### 2、语法规则

1. 不能更改变量为非原有类型的数据。ps: 强类型语言,不会像JavaScript一样进行隐式类型转换。推荐写法：`let num: number = 25;` 把数据类型标注出来，比较清晰。

### 3、基本数据类型

1. Number: 整型，浮点型，二进制，八进制，十六进制 `let num: number = 25; || let num = 25;`
2. bboolean: 布尔值。 `let bo: boolean = false;`
3. string: 字符串 `let str: string = 'hello';`
4. any：任何类型。类似于js了 `let anything: any = 任何值` 尽量不用

### 4、 数组

1. 使用 `let arr:Array<number> = [1,2.1,3,5,6] || let arr: number[] = [1,2.1,3,5,6]`
2. 声明一个数组类型，需要在`<>`里面声明数组值的类型
3. 可以通过数组的下标拿到值
4. 修改：`arr[0] = 'abc'`
5. any类型可以存储任何类型。 `let anyArr: any[] = [1, '1', true]; `

### 5、元组

1. 数组的每一项的类型和值需一致 `let colors: [string, number] = ['red', 255];`


### 6、枚举 enum

它用于声明一组命名的常数，当一个变量有几种可能的取值时，可以将它定义为枚举类型。通常只将枚举元素数值赋给枚举类型的变量,但也可以将任何长整数数值赋给该变量。

1. 可以通过`enum`关键字自己定义类型

```
  enum Color{
    black,  // 0
    yellow = 100, // 100
    red, // 101
    ...
  }

  let myColor: Color = Color.balck

  console.log(myColor);  // 0
```


### 7、函数

可以规定函数的数据类型，如果不定义则默认为any类型，不推荐。

1. 函数的返回值

```

  function returnVal(): number{
    return 22;
  }

  // 如果没有返回值，则返回值的类型就是void

  function func(): void{
    console.log('hello cat');
  }
```

a. 参数类型
```
  function sumVal(val1:number, val2:number): number{
    return val1 + val2;
  }
```

b. 函数类型
```
  // 定义一个函数类型，参数为两个number类型的参数，返回值也需要为number类型
  let myFunc: (a:number, b:number) => number
  myFunc = sumVal;  // sumVal 是上面定义的函数
  myFunc(2,3); // 5
```

### 8、object  & type
ts的对象定义的时候，如果定义的赋值里面有值，则值成为它的类型的一部分,且属性名为必选参数
```
  let obj = {
    str: '123',
    num: 123
  }
  // 相当于定义了一个对象为
  let obj2: {str:string, num:number} = {
    str: '123',
    num: 123
  }
```

这个时候如果再去给`obj`赋值一个空对象
```
  obj = {};
```

会报错:`Type '{}' is missing the following properties from type '{ str: string; num: number; }': str, numts(2739)`

需要更改的话只能
```
  obj = {
    str: '456',
    num: 456
  }
```

如果赋值为别的key的话会报错
```
  obj = {
    str: '456',
    aaa: '456'
  };
  // 不能将类型“{ str: string; aaa: string; }”分配给类型“{ str: string; num:number; }”。 对象文字可以只指定已知属性，并且“aaa”不在类型“{ str: string；num: number; }”中。
```

想要多加其他的属性也是不可以的。

复杂一点的实现
```
  let obj: {data: number[], myFunc:(item:number) => number[]} = {
    data: [1,2,3],
    myFunc: function(item: number) {
      this.data.push(item)
      return this.data
    }
  }
  console.log(obj.myFunc(2)) // [ 1, 2, 3, 2 ]
```

type关键字实现上面的功能，用于应对复杂情况
```
  type myType = {data: number[], myFunc:(item:number) => number[]};
  let obj:  myType= {
    data: [1,2,3],
    myFunc: function(item: number) {
      this.data.push(item)
      return this.data
    }
  }
  console.log(obj.myFunc(2))
```

### 9、联合类型
```
  let unionType = number | string = 12;
  unionType = '12';
```

### 10、检查类型

使用`typeof`进行判断。
```
  let num = 12;
  if(typeof num == "number"){
    console.log(num);
  }
```

### 11、null undefined
申明一个变量，如果初始化的时候有赋值为某一个类型`let num = 12;`，则再赋值null或者undefined的时候会报错，可以在`tsconfig.json`里面把配置修改了，找到`"strict": true,  `修改为false就可以了。 

初始化为null 和 undefined可以进行这两个的赋值。
```
  let a = null;
  a = undefined;
```

### 12、never类型
1. never是任何类型的子类，也可以赋值给任何类型，但是没有任何类型可以赋值给never(除了never本身)。
2. 通常使用在抛出异常或执行到终点。


### 13、类型使用demo

```
  type sumValueType = {money:number, count: (value: number) => void}

  let sumValue: sumValueType= {
    money: 200,
    count: function(value: number): void{
      this.money += value;
    }
  }

  let handleCount:{name: string; sumValue:sumValueType; friends: string[]} = {
    name: 'henry',
    sumValue: sumValue,
    friends: ['bucky', 'elyse']
  }

  handleCount.sumValue.count(500);
```

### 14、类的属性和方法
ts的类和es6的大致一致，多了一些类型和关键字

```

  class Person {
    public name: string;  //共有的  默认是public
    protected gender: string;  // 受保护的，只能当前类或者其子类使用
    private age: number = 22; // 私有的 只能当前类使用，子类也不可以

    // 可以在构造函数里面定义变量，就不用在上面定义了，效果是一样的
    constructor(name: string, gender: string, public nickname: string){
      this.name = name;
      this.gender = gender;
      this.nickname = nickname;
    }

    setName(name: string) {
      this.name = name;
      console.log(this.name);
    }
  }

  const person = new Person("cat", "男", "miao~")

  console.log(person.nickname) // miao~

  
  person.setName("said"); // said

  // 在外部使用person.gender会报错，因为不能在外部访问

```

### 15、类的继承

```

  class Student extends Person{
    constructor( name: string, nickname: string){
      super(name, '男', nickname);
      // 不能调用父类的age属性，因为是私有的。 gender可以。
    }
  }

  const student = new Student("cat", "cccc");
  console.log(student.name, student.nickname); // cat cccc

```

### 16、set get static

#### 16.1 set get
  修饰词用于隔离私有属性和可公开属性。
  
```
  class Person1 {
    private _name: string = 'coral'

    // 私有属性赋值
    set setName(val: string){
      this._name = val;
    }

    get getName(): string{
      return this._name;
    }

  }

  let p1 = new Person1();

  console.log(p1.getName) // coral

  p1.setName = 'xrr';

  console.log(p1.getName) // xrr
```

#### 16.2 static 静态属性

```
  class Person1 {

    static PI: number = 3.14;

  }

  console.log(Person1.PI)
```

### 17、namespace

####  17.1 定义
  TypeScript 的命名空间只对外暴露需要在外部访问的对象，防止变量污染，命名空间内的对象通过` export `关键字对外暴露。

```
  namespace Mymath  {
    const PI = 3.14;

    export function sumVal(val1: number, val2: number): number{
      return val1 + val2;
    }
  }

  console.log(Mymath.sumVal(5, 10));
```

####  17.2 使用：文件分离

首先，新建三个文件

文件1 `namespace.ts`
```
  console.log(Mymath.sumVal(5, 10));
  console.log(Mymath.getVal(666));
```

文件2 `sumVal.ts`
```
  namespace Mymath  {
    export function sumVal(val1: number, val2: number): number{
      return val1 + val2;
    }
  }

```


文件3`getVal.ts`
```
  namespace Mymath  {
    export function getVal(val1: number): number{
      return val1;
    }
  }

```

文件合成，打开命令行工具输入
```
  tsc --outfile namespaceTest.js sumVal.ts getVal.ts namespace.ts
```
`namespaceTest.js` 是另外三个文件要合成的名字。

运行
```
  node namespaceTest.js
  // 15
  // 666
```
`namespaceTest.js` 合成的文件的代码

```
  var Mymath;
  (function (Mymath) {
      function sumVal(val1, val2) {
          return val1 + val2;
      }
      Mymath.sumVal = sumVal;
  })(Mymath || (Mymath = {}));
  var Mymath;
  (function (Mymath) {
      function getVal(val1) {
          return val1;
      }
      Mymath.getVal = getVal;
  })(Mymath || (Mymath = {}));
  console.log(Mymath.sumVal(5, 10));
  console.log(Mymath.getVal(666));
```

####  17.3 多重命名空间

  命名空间里面还有其他命名空间的时候，需要使用`export`把命名空间也暴露出来。

```
  namespace cat1 {
    let PI: number = 3.14;

    export function getPI(val: number):number {
      return val;
    }

    export namespace cat2 {
      export function sumVal(val1: number, val2: number): number {
        return val1 + val2;
      }
    }
  }

  console.log(cat1.getPI(2)); // 2
  console.log(cat1.cat2.sumVal(1,2)); // 3
```

####  17.4 引入文件
  
引入文件可以使用`/// <reference path="filePath" />`语法引入

引入：
```
  /// <reference path="getVal.ts" />
  /// <reference path="sumval.ts"/>
  console.log(Mymath.sumVal(5, 10));
  console.log(Mymath.getVal(666));
```

打包：用引入文件的方式打包就不需要打那么多文件名字了，但是需要注意`--outFile`的`F`是大写的。
```
  tsc namespace.ts --outFile namespace.js
```

运行：
```
  node namespace.js
```

### 18、模块module
可以是配置里选择使用`commonjs` or `system` or `amd` or `es2015`...

新建两个文件,`a.ts`, `b.ts`

在`a.ts`里面输入

```
  export let PI: number = 3.14;
```

在`b.ts`里面输入

```
  import {PI} from './module'
  console.log(PI);
```

打开命令行工具，编译并运行

```
  tsc b.ts
  node b.js //3.14
```
上面指的是运行在`node.js`环境里面的，由于js不支持模块化，有一个解决办法就是`system`
在浏览器环境下运行的话需要在HTML文件里面引入`system.js`的文件
CDN: `https://cdn.bootcss.com/systemjs/0.21.5/system.js`

在HTML文件下面需要设置一下配置
```
  <script>
    System.config({
      baseUrl: "/",
      package: {
        "/":{
          "defaultExtension": "js"
        }
      }
    })

    System.import('b.js');
  </script>
```

扩展一下写法：
1. `import * as P from 'b.js'`  调用：`P.PI`
2. 在导出的地方`export default function sumVal(){....}`； 引入：`import sum from './sumVal'`   调用：`sum(1,2);`


### 19、interface  接口

interface 和 type关键字差不多，最大的差别是，interface可以继承，type不可以
实现interface接口需要把interface定义的非选填的(`?:`)全部实现

```
  interface Person2 {
    name: string;
    age: number;
    sex?: string;  // ?: 为选填
    readonly salary: number;  //readonly关键字描述的为只读属性
    [propName: string]: any;  // 任意名字 任意类型的值
    greet(): void; // 定义一个方法，莫得返回值，有返回值参照上面函数部分设置
  }

  let person: Person2 = {
    name: 'catsaid',
    age: 18,
    salary: 1234,
    id: 9527,
    greet():void{
      console.log('hi person');
    }
  }
  person.greet(); 
  console.log(person) 

  // 运行
  node interface.js
  // hi person
  // ....
```

### 20、接口的实现、继承

#### 20.1 implements： 实现

```
  interface PersonInterface {
    name: string;
    age: number;
    sex?: string;  // ?: 为选填
    readonly salary: number;  //readonly关键字描述的为只读属性
    [propName: string]: any;  // 任意名字 任意类型的值
    greet(): void; // 定义一个方法，莫得返回值，有返回值参照上面函数部分设置
  }

  class People implements PersonInterface{
    name: string = 'catsaid';
    age: number = 22;
    salary: number = 8880;
    greet(): void {
      console.log(this.name);
    }
  }

  let cat = new People();
  cat.greet(); // catsaid

```

class 可以同时实现多个interface

```
  class People implements PersonInterface, secondInterface{
    ....
  }
```

#### 20.2 继承

```
  interface Employee extends PersonInterface{
    ...
    work: string = 'monkey'; // 除继承的之外自己有的属性
  }
```

### 21、 泛型 Generic

使用泛型来创建可重用的组件，一个组件可以支持多种类型的数据。 这样就可以以自己的数据类型来使用组件。

#### 21.1 泛型函数
定义一个泛型的函数
```
  function identity<T>(arg: T): T {
    return arg;
  }
```

指定类型调用

```
  identity<string>("myString");
```

不指定类型调用，会根据传入的值自动进行判断是什么类型,但是使用泛型的时候，必须正确使用这个通用的类型， 换句话说，你必须把这些参数当做是任意或所有类型。

```
  identity("myString");
```

#### 21.2 泛型接口

指定成员类型为泛型，或者指定接口类型为泛型

```
  interface genericIdentify {
    <T>(arg: T): T;
  }

  function identify<T>(arg: T): T{
    return arg;
  }

  let myIdentify: genericIdentify = identify;

  let str = myIdentify<string>('hi');
  console.log(str);  // hi

  // -----------------------------------

  interface genericIdentify<T> {
    (arg: T): T;
  }

  function identify<T>(arg: T): T{
    return arg;
  }

  let myIdentify: genericIdentify<string> = identify;

  let str = myIdentify('hi');
  console.log(str);  // hi

```

#### 21.3 为泛型添加约束

有时候需要为泛型的添加固定的某个条件,如下必须有一个`length`属性

```
  // 约束了泛型函数的参数里面必须有length属性，类型是any，可以自定义
  function getLength<T extends {length: any}>(obj: T):any {
    return obj.length;
  }

  const objLength = {
    length: '111'  // 必须带，不带就报错
  }
  console.log(getLength(objLength)
```

也可以指定必须为某种类型

```
  function getLength<T extends number>(obj: T):any {
    return obj;
  }

  let objLength = 22
  console.log(getLength(objLength)
```

#### 21.4 泛型类
和泛型接口差不多

```
  class CountNum<T> {
    num1: T;
    num2: T;

    constructor(num1: T, num2: T){
      this.num1 = num1;;
      this.num2 = num2;
    }

    sumNum(): number {
      // +号为必填，不加会报错
      return +this.num1 + +this.num2;
    }
  }

  let countNum = new CountNum<number>(10, 20);
```

可以为泛型类添加约束

```
  class CountNum<T extends number>{...}

  // 添加约束，只能传number类型的值，实例化的时候就只能传number了;
  let countNum =new CountNum<number>(10, 20);
```



