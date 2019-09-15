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

> typescript是JavaScript的超集。

> typescript运行在浏览器的话需要一些插件转换，因为浏览器只认前端三剑客。

> typescript是强类型语言。

> 是属于微软的，所以推荐用VSCODE编辑器

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




