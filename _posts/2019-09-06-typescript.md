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

2. 参数类型
```
  function sumVal(val1:number, val2:number): number{
    return val1 + val2;
  }
```

3. 函数类型

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